# LangChain Tools and Agents: Summary Cheat Sheet

## 1. Tools
Tools are modular functions, APIs, or computational modules made available to LLMs so they can interact with real-world data and external systems.

### Core Schema Components in LangChain:
* **Name**: A unique identifier for the tool.
* **Description**: A brief explanation of the tool's purpose and when to use it.
* **Parameters**: The expected inputs required for the tool to function correctly.

### Tool Initialization Methods:
* **Built-in Tools**: Ready-to-use utilities for common tasks (e.g., `WikipediaQueryRun`).
* **`load_tools` Function**: Conveniently loads multiple preset tools like Wikipedia, serpapi, or llm-math.
* **Custom Tools**: Defined using the `Tool` class or the `@tool` decorator to wrap any custom function.
* **OpenAI Functions Integration**: Converted via `convert_to_openai_function`, `bind_functions`, or `bind_tools` for model-native bindings.

---

## 2. Tool Calling vs. Function Calling
* **Tool Calling**: A broad industry term used across providers (like Anthropic) and frameworks (like LangChain).
* **Function Calling**: The specific terminology popularized by OpenAI in their API documentation.
* *Note:* Both terms are technically identical, describing how an LLM generates structured representations (JSON) requesting external functions to be executed with specific parameters. The LLM does not execute functions directly; an external system handles execution and feeds results back.

---

## 3. AI Agents
Agents are high-level orchestration systems powered by LLMs that make dynamic decisions, reason, use tools, access memory, and take actions to solve complex, multi-step problems.

### Core Architecture Components of a LangChain Agent:
1. **AI Agent**: The main intelligence unit incorporating the LLM, logic, tools, and memory.
2. **Large Language Model (LLM)**: The core reasoning engine that interprets queries, plans steps, and decides whether to recall memory, invoke a tool, or answer directly.
3. **Tool(s)**: External capabilities and functions invoked via structured requests.
4. **Memory**: Long-term or short-term context storage (RAM, SQL, or VectorDBs) ensuring personalization and conversation continuity.
5. **Action**: The execution step where a structured LLM request translates into a real-world system operation.
6. **External World**: APIs, operating systems, the internet, or physical devices accessed via tools.

---

## 4. Building and Executing Agents in LangChain
* **Built-in Agent Types**: Predefined types like `zero-shot-react-description` and `chat-zero-shot-react-description` for task-based tool selection.
* **OpenAI Function Agents**: Created using `create_openai_functions_agent` for predictable interaction with structured tools.
* **LangGraph Orchestration**: Low-level flexibility utilizing high-level methods like `create_react_agent` for complex reasoning and tool chaining.
* **AgentExecutor**: The runtime execution manager that handles the loop of calling the LLM, processing tool outputs, and evaluating final responses.
* **Memory Integration**: Utilizing components like `MemorySaver` to maintain context across multi-turn user interactions.
