# Study Guide: FastMCP Server Development & Advanced Capabilities

This guide covers building, testing, and enhancing MCP servers using **FastMCP**, incorporating advanced features like progress reporting, logging, user elicitation via MCP Context, and integrating multi-server setups with ReAct agents.

---

## 1. Creating MCP Servers with FastMCP

FastMCP provides an intuitive interface for constructing servers and defining primitives using standard Python syntax and decorators.

### Server Initialization

```python
from fastmcp import FastMCP

# Instantiate FastMCP server
# Instructions provide natural language docs for LLMs to understand server capabilities
mcp = FastMCP(
    name="CalculatorServer",
    instructions="Provides basic arithmetic tools and static resources."
)
```

---

## 2. FastMCP Server Primitives

### A. Tools (`@mcp.tool`)
Tools allow LLMs to execute functions that modify state, run calculations, or interact with external systems.

* **Implementation Pattern:**
  * Use the `@mcp.tool()` decorator.
  * Define explicit Python type hints for arguments and return types.
  * Write clear docstrings—the LLM reads docstrings to determine tool selection.

```python
@mcp.tool()
def add(a: int, b: int) -> int:
    """Adds two integers together and returns the sum."""
    return a + b

@mcp.tool()
def subtract(a: int, b: int) -> int:
    """Subtracts parameter b from parameter a."""
    return a - b
```

### B. Resources (`@mcp.resource`)
Resources act as "read-only filing cabinets" exposing passive data access via **URIs**.

* **URI Scheme & Conventions:**
  * URIs use custom prefixes defined by developers (e.g., `docs://`, `dir://`, `file:///`).
  * Note on local files: `file:///` uses **three slashes** because `file://` (two slashes) is a reserved URI scheme expecting an explicit host name.
* **Resource Templates:** Parameterized URIs allow dynamic parameter extraction.

```python
import os

BASE_DIR = os.path.dirname(os.path.abspath(__file__))

def get_path(rel_path: str) -> str:
    return os.path.abspath(os.path.join(BASE_DIR, rel_path))

# Resource Template with dynamic path parameter
@mcp.resource("file:///{filepath}")
def read_local_file(filepath: str) -> str:
    """Fetches text contents of a file relative to the project directory."""
    full_path = get_path(filepath)
    if os.path.isfile(full_path):
        with open(full_path, "r") as f:
            return f.read()
    return f"Error: File '{filepath}' not found."

# Static Resource returning directory contents
@mcp.resource("dir://current")
def list_directory() -> dict:
    """Returns a list of items and metadata in the current workspace directory."""
    items = os.listdir(BASE_DIR)
    return {"items": items}
```

### C. Prompts (`@mcp.prompt`)
Prompts are reusable templates designed for user-controlled workflows to guide LLM interactions.

```python
@mcp.prompt()
def code_review(filepath: str) -> str:
    """Generates a structured code review prompt for a specified target file."""
    full_path = get_path(filepath)
    if not os.path.exists(full_path):
        return f"Error: File {filepath} does not exist."
    
    with open(full_path, "r") as f:
        code_content = f.read()
        
    return f"Please review the following code file ({filepath}) for potential bugs and quality improvement:\n\n{code_content}"
```

---

## 3. Advanced Context Capabilities

The `Context` object in FastMCP gives primitives access to underlying session utilities, enabling enhanced real-time interaction:

1. **Logging:** Send operational or error logs back to the client context.
2. **Progress Reporting:** Transmit chunked execution status for long-running operations.
3. **User Elicitation (`ctx.elicit`):** Prompt the user directly via the client to supply missing parameters or clarifications dynamically during execution.

```python
from fastmcp import Context

@mcp.tool()
async def write_file_with_progress(filepath: str, contents: str, ctx: Context) -> str:
    """Writes contents to a file while logging messages and reporting progress back to client."""
    full_path = get_path(filepath)
    
    # 1. Log action
    ctx.info(f"Starting write operation to: {filepath}")
    
    # 2. Ensure parent directories exist
    os.makedirs(os.path.dirname(full_path), exist_ok=True)
    
    # 3. Write in chunks & report progress
    chunk_size = 100
    total_len = len(contents)
    
    with open(full_path, "w") as f:
        for i in range(0, total_len, chunk_size):
            chunk = contents[i:i+chunk_size]
            f.write(chunk)
            # Report progress (current, total)
            await ctx.report_progress(i + len(chunk), total_len)
            
    ctx.info(f"Successfully wrote {filepath}")
    return f"File successfully written to {filepath}"
```

---

## 4. MCP Testing & Transport Setup

MCP servers can be exposed across three transport channels:

### A. In-Memory Transport (Development & Unit Testing)
Ideal when client and server run inside the same Python process.

```python
import asyncio
from fastmcp import Client

async def test_in_memory():
    # Pass server object directly to client
    client = Client(mcp)
    async with client:
        result = await client.call_tool("add", {"a": 4, "b": 5})
        print("In-Memory Result:", result)

asyncio.run(test_in_memory())
```

### B. Standard Input/Output (STDIO Transport)
Launches the server as a local child process communicated via `stdin`/`stdout` pipes.

```python
# In stdio_server.py
if __name__ == "__main__":
    # Runs the server on standard input/output
    mcp.run()
```

### C. Streamable HTTP Transport
Runs the server as an asynchronous network endpoint accessible remotely over HTTP.

```python
# Starts an HTTP server running on port 8000
if __name__ == "__main__":
    mcp.run_http_async(port=8000)
```

---

## 5. Connecting Agents to MCP Servers

### Single HTTP Transport Agent Setup

```python
import asyncio
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent
from fastmcp.client.transports import streamable_http_client, client_session
from fastmcp.adapters.langchain import load_mcp_tools

async def run_agent():
    # 1. Open HTTP connection streams
    async with streamable_http_client("http://localhost:8000/mcp") as (read, write, _sid):
        # 2. Manage MCP session
        async with client_session(read, write) as session:
            await session.initialize()
            
            # 3. Load & adapt MCP tools to LangChain format
            tools = await load_mcp_tools(session)
            
            # 4. Instantiate ReAct Agent
            llm = ChatOpenAI(model="gpt-4o", temperature=0)
            agent = create_react_agent(llm, tools)
            
            # 5. Invoke agent
            response = await agent.ainvoke({"messages": [("user", "Add 1 and 2 together.")]})
            print(response["messages"][-1].content)

asyncio.run(run_agent())
```

### Multi-Server Setup with `MultiServerMCPClient`

`MultiServerMCPClient` allows agents to connect across multiple HTTP endpoints and STDIO subprocesses seamlessly through a unified configuration dictionary.

```python
import asyncio
from langchain_openai import ChatOpenAI
from langgraph.prebuilt import create_react_agent
from mcp_client import MultiServerMCPClient

async def main():
    server_configs = {
        # STDIO Subprocess configuration
        "calculator": {
            "transport": "stdio",
            "command": "python",
            "args": ["stdio_server.py"]
        },
        # Remote HTTP Endpoint configuration
        "remote_service": {
            "transport": "http",
            "url": "http://localhost:8000/mcp"
        }
    }

    async with MultiServerMCPClient(server_configs) as mcp_client:
        # Retrieve combined tool list across all connected servers
        tools = await mcp_client.get_tools()
        
        llm = ChatOpenAI(model="gpt-4o", temperature=0)
        agent = create_react_agent(llm, tools)
        
        response = await agent.ainvoke({"messages": [("user", "Use the calculator server to add 15 and 27.")]})
        print(response["messages"][-1].content)

if __name__ == "__main__":
    asyncio.run(main())
```

---

## 6. Summary Comparison Table

| Feature / Primitive | Primary Purpose | Key Decorator / Class | Key Trait |
| :--- | :--- | :--- | :--- |
| **Tool** | Side-effects, calculations, actions | `@mcp.tool()` | Callable functions with input standard schema |
| **Resource** | Exposing passive read-only data | `@mcp.resource()` | Accessed via URIs (e.g. `file:///`, `dir://`) |
| **Prompt** | Standardized workflow templates | `@mcp.prompt()` | User-driven macro templates |
| **Context** | Logging, progress, dynamic input | `Context` | Real-time session interface |
| **In-Memory** | Same-process testing | `Client(mcp)` | Instant local connection |
| **STDIO** | Local machine process | `mcp.run()` | Subprocess execution over `stdin`/`stdout` |
| **Streamable HTTP**| Remote network access | `mcp.run_http_async()` | HTTP POST with streaming SSE options |