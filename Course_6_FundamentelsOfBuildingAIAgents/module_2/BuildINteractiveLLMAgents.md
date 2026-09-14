# Building Interactive LLM Agents: Tool Extraction, Manual Execution, and Agent Classes

## 1. Structuring Interaction & Chat History
- **Chat History Initialization:** To give the model complete context during a conversation, user inputs, model responses, and tool outputs are stored in a list called `chat_history`[cite: 1].
- **HumanMessage:** Plain text user queries (e.g., *"What is 3 plus 2?"*) are converted into a `HumanMessage` symbol wrapper from LangChain core messages, indicating to the model that the text originated from the user[cite: 1].

---

## 2. Extracting Tool Calls and Arguments
- **Model Output (`AIMessage`):** When passing the complete chat history into the LLM with tools, the model reviews the context, identifies available tools, selects the right one, and outputs an `AIMessage` containing a `tool_calls` array instead of plain text[cite: 1].
- **Key Details in Tool Call Parameters:**
  - **Name:** The tool the model wants to call (e.g., `"add"`)[cite: 1].
  - **Arguments:** A JSON string specifying parameters to pass into the tool (e.g., `{"a": 3, "b": 2}`)[cite: 1].
  - **ID:** A unique identifier linking the response back to the specific request, which is especially critical when multiple tools are invoked simultaneously[cite: 1].
  - **Type:** Specifies that the output is a tool call rather than standard text[cite: 1].

---

## 3. Manual Tool Execution and Response Integration
1. **Extraction:** Extract the tool's name, arguments (`args`), and tool call `ID` from the model's instructions[cite: 1].
2. **Execution via Tool Map:** Use a pre-defined tool mapping dictionary where the key corresponds to the tool name and the values correspond to the parsed parameters to execute the function manually (e.g., yielding `5` for $3 + 2$)[cite: 1].
3. **ToolMessage:** Wrap the tool's execution output inside a `ToolMessage` object, ensuring the tool call `ID` links the result back to the model's original request[cite: 1].
4. **Updating History & Final Generation:** Append the `ToolMessage` to `chat_history` and pass the updated history back to the LLM via `.invoke()`, allowing the model to generate a natural language final response based on the tool's output[cite: 1].

---

## 4. Encapsulating Logic in an Agent Class
- **`ToolCallingAgent`:** You can build an agent class that encapsulates the entire process—binding tools to the LLM, managing chat history, extracting names/arguments, and handling manual or automated tool execution[cite: 1].
- **Robustness:** A well-structured agent class allows the LLM to process imperfect or imprecise natural language queries (e.g., understanding intent from queries like `"1 minus 2"`) and execute the correct tool automatically[cite: 1].
