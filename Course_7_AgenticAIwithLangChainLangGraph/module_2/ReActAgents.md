# ReAct Agents: Building Agents That Reason Before Acting


## 1. Core Principles of ReAct Agents
* **Step-by-Step Reasoning:** ReAct agents are designed for complex tasks that require iterative reasoning rather than single-pass generation.
* **Structured Output Pattern:** System messages instruct the LLM to format its execution path using a strict cognitive sequence:
  1. **Thought:** The LLM reasons about what to do next.
  2. **Action:** The specific name of the tool to use.
  3. **Action Input:** The input provided to the selected tool.
  4. **Observation:** The result or data returned by the tool after interacting with the environment.
  5. **Final Answer:** The complete response delivered to the user once all necessary information is gathered.
* **Environmental Interaction:** By leveraging tools (such as weather APIs or clothing recommenders), the LLM queries the external environment, ingests tool observations into its chat history, and loops until no further tool calls are required.

---

## 2. Implementing a ReAct Agent in LangGraph

### State Management & Setup
* **Message State:** The agent state schema uses a message history dictionary where the `messages` key stores a growing list of exchanges (`HumanMessage`, `AIMessage`, or `ToolMessage`).
* **Annotated Reducers:** Using `Annotated` with `add_messages` ensures that new messages are automatically appended each time a graph node executes.
* **Tools and Prompting:** Define tools (e.g., wrapping a Tavily search tool with `@tool` or creating custom clothing recommendation logic) and establish a system message instructing the agent to think step-by-step and use tools when necessary.

### Graph Construction and Routing
1. **Nodes:** 
   * An `agent` node that passes the state to the model and appends the LLM's response to the message history.
   * A `tools` node (`tool_node`) that executes requested tool calls and wraps results in tool messages.
2. **Conditional Edge Control (`should_continue`):** 
   * Inspects the latest message from the agent state.
   * If the message contains no tool calls, it routes to the `END` node.
   * If tool calls are present, it routes to the `tools` node to execute the request, feeding the observation back into the agent's reasoning loop.

### Execution Flow Example
* **User Query:** *"What's the weather like in Zurich? And what should I wear based on the temperature?"*
* **Step 1:** The LLM generates a thought and issues a tool call to search for Zurich's weather.
* **Step 2:** The search tool returns the observation (temperature/conditions).
* **Step 3:** The LLM processes the observation, issues a second tool call to the clothing recommendation tool, and receives the next observation.
* **Step 4:** Having acquired all necessary data, the LLM outputs the final answer, terminating the graph flow.
