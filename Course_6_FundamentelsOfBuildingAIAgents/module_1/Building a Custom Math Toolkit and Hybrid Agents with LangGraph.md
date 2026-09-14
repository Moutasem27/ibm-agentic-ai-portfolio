# Building a Custom Math Toolkit and Hybrid Agents with LangGraph

## Learning Objectives
By the end of this material, you will be able to:
- Build ReAct-style agents using the `create_react_agent` method for greater customization and control[cite: 1].
- Construct a multi-tool math assistant by combining tools for addition, subtraction, multiplication, and division[cite: 1].
- Guide agent behavior using custom prompts and structured tool inputs[cite: 1].
- Orchestrate multiple tools within a single agent to handle real-world multi-step queries[cite: 1].
- Extend agent functionality by integrating external tools, such as Wikipedia Search, for dynamic, hybrid responses[cite: 1].

---

## 1. Moving to LangGraph for Advanced Flexibility
- **Beyond `initialize_agent`:** While `initialize_agent` provides a convenient starting point with predefined strategies (like ReAct or Structured Chat), **LangGraph** has rapidly become the preferred, robust approach for building multi-step agent workflows[cite: 1].
- **`create_react_agent`:** Using the `create_react_agent` function from the `langgraph.prebuilt` module allows you to work with a single agent type while gaining complete customization over the prompt template and reasoning style[cite: 1].
- **Invocation & Debugging:** Agents are interacted with via the `.invoke()` method by passing a dictionary with a `messages` key containing role-message pairs (e.g., human queries)[cite: 1]. The response object retains rich trace information including tool call histories and intermediate reasoning steps for debugging[cite: 1].

---

## 2. Building a Custom Math Toolkit
Real-world tasks often require more than a single tool, necessitating specialized toolkits (such as separate functions for deposits, withdrawals, and transfers in banking)[cite: 1]. 
- **Math Toolkit:** Moving beyond simple addition, a robust math assistant combines multiple operations into a `tools` list[cite: 1]:
  - Addition tool[cite: 1]
  - Subtraction tool[cite: 1]
  - Multiplication tool[cite: 1]
  - Division tool[cite: 1]
- **Execution Flow:** When a user queries a multi-step math problem (e.g., multiplying 2, 3, and 4), the agent evaluates the input, selects the appropriate tool, parses parameters, verifies the tool output, and formats the final answer[cite: 1].

---

## 3. Integrating Pre-Built and Custom External Tools
LangChain offers a rich ecosystem of out-of-the-box pre-built tools for common operations[cite: 1]:
- `WikipediaQueryRun`: Searches Wikipedia for factual information[cite: 1].
- `GoogleSearchRun`: Performs web searches using Google's API[cite: 1].
- `PythonREPLTool`: Runs Python code safely for complex logic or calculations[cite: 1].
- `OpenWeatherMapQueryRun`: Fetches live weather data[cite: 1].
- `YouTubeSearchTool`: Searches for videos on YouTube[cite: 1].

### Creating Custom Search Tools:
- You can create custom wrappers (e.g., wrapping `WikipediaQueryRun` using the `@tool` decorator) by defining clear input/output types and a detailed docstring explaining parameters and return values[cite: 1].

---

## 4. Orchestrating Hybrid Agents (Math + Retrieval)
- **Hybrid Toolkits:** By updating your `tools` list to combine calculation tools (math) and information retrieval tools (like Wikipedia search), you enable the agent to handle complex, multi-step hybrid queries[cite: 1].
- **Example Flow:** When given a query like *"What is the population of Canada, multiply it by 0.75"*, the agent first invokes `Search_Wikipedia` to retrieve Canada's population, passes that numerical result into the multiplication tool, and finally synthesizes a natural language response with the calculated result[cite: 1].
