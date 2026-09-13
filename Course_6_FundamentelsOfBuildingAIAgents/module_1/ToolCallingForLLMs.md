# Tool Calling: Traditional vs. Embedded Approaches

## What is Tool Calling?
- Tool calling is a technique that makes an LLM context-aware of real-time data, such as databases or APIs, typically used via a chat interface[cite: 1].
- **Traditional Workflow:** 
  1. The client application sends a set of messages and a list of tool definitions to the LLM[cite: 1].
  2. The LLM reviews the message and tool list, then recommends a tool to call[cite: 1].
  3. The client application executes the tool and sends the response back to the LLM[cite: 1].
  4. The LLM interprets the tool response to either recommend the next tool or provide the final answer[cite: 1].

### Tool Definition Components:
- **Name:** The identifier of every tool[cite: 1].
- **Description:** Additional information on how or when to use the tool[cite: 1].
- **Input Parameters:** The required parameters needed to make a tool call[cite: 1].
- **Tool Types:** Can include APIs, databases, or code interpreted via a Code Interpreter[cite: 1].

---

## Traditional vs. Embedded Tool Calling

### 1. Traditional Tool Calling & Its Flaws
- **Example:** Asking for the temperature in Miami causes the LLM to recommend a weather API tool call based on the provided tool definition, which the client then runs and returns (e.g., 71 degrees) for the LLM to formulate a final answer[cite: 1].
- **Flaws:** The LLM can hallucinate or make up incorrect tool calls[cite: 1].

### 2. Embedded Tool Calling
- Uses a library or framework sitting between the application and the LLM to interact with both tool definitions and execution[cite: 1].
- **Workflow with a Library:**
  1. Messages from the application pass through the library[cite: 1].
  2. The library appends the tool definitions and sends the combined message and tools to the LLM[cite: 1].
  3. Tool call outputs are sent directly back to the library for execution rather than back to the user application first[cite: 1].
  4. The library handles tool execution and retries calls if necessary, preventing hallucinations and providing the final answer directly to the application[cite: 1].

---

## Summary
- Tool calling connects LLMs to real-world external resources like APIs, databases, and code interpreters[cite: 1].
- While traditional tool calling directly exposes tool definitions and execution handling to the client application, it remains vulnerable to hallucinations and incorrect tool calls[cite: 1].
- Embedded tool calling mitigates these issues by inserting a governing library to handle tool execution, error retries, and accurate output generation[cite: 1].
