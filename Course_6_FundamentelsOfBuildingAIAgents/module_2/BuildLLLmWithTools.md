# Building LLM Agents with Tools: Initialization, Binding, and Dynamic Execution


## 1. Initializing the Chat Model for Tool Interactions
- **Setup:** Transform a basic model into an interactive agent by setting up a chat model for tool interactions[cite: 1].
- **Initialization:** Import `initChatModel` from LangChain's `ChatModels` module to handle the required setup connection[cite: 1]. Load `gpt-4o-mini` with `model_provider="openai"` to create an object that sends and receives messages (referred to as the `llm` object)[cite: 1].

---

## 2. Defining and Binding Custom Tools
- **Creating Custom Tools:** Use the `@tool` decorator to let LangChain know a Python function can be called by the model[cite: 1]. For example, define an `add` function taking two integers (`a` and `b`) and returning their sum[cite: 1]. The function's docstring is utilized by the model to determine when to trigger the tool[cite: 1].
- **Binding to the LLM:** Place the tool in a list and bind it to the chat model using `.bind()` (or similar binding methods) to create a new object (e.g., `llmWithTools`)[cite: 1]. This ensures the model recognizes and uses the tool whenever it needs to compute a sum[cite: 1].
- **Expanding Functionality:** Add more custom operations to the tool list, such as subtraction and multiplication functions[cite: 1].

---

## 3. Dynamic Function Calls Using Mapping Dictionaries
- **Mapping Dictionaries:** When you need to call a function dynamically using its string name, create a mapping dictionary that links tool names as strings to their corresponding tool functions[cite: 1].
- **Execution Flow:** 
  1. Define input arguments as a dictionary where keys match parameter names (e.g., `{"a": 1, "b": 2}`)[cite: 1].
  2. Use the tool name string as a key to access the function from the mapping dictionary[cite: 1].
  3. Invoke the function using the input dictionary, which automatically matches the keys to parameters and executes the tool[cite: 1].

---

## 4. Summary & Context Management
- Integrating custom tools turns static language models into dynamic agents capable of processing real-world math and logic[cite: 1].
- Managing chat history alongside tool execution preserves conversation context for more accurate and personalized interactions[cite: 1].
