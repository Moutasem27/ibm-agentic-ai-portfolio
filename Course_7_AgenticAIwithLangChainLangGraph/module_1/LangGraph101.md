# Getting Started with LangGraph 101: Core Concepts and Application Structure


## 1. Defining State in LangGraph
* **What is State?** State is a complex, evolving memory that holds all graph inputs, intermediate values, and outputs. It can consist of integers, random values, lists, nested structures, or message sequences.
* **TypedDict Usage:** State variables (such as counter integers and strings) are commonly defined using a `TypedDict` subclass from the `typing` module, acting as a dictionary with typed information to support complex types.

---

## 2. Nodes, Functions, and Side Effects
* **Processing Nodes:** LangGraph nodes link to functions that take the state as input, perform computation, and return updated state dictionaries (e.g., incrementing a counter `n` by 1 and generating a random lowercase letter).
* **Side-Effect Nodes:** Some nodes (like print functions) take the state object as input, print values, and return the state unchanged, serving solely for side effects rather than data modification.
* **State Updates:** Depending on the graph type, returned keys and values update the state (some graphs require full returns, while others automatically merge updates or drop missing fields).

---

## 3. Building the State Graph
LangGraph workflows are built and compiled using structured primitives:
* **`StateGraph` Initialization:** Create a state graph object initialized with your `TypedDict` state class.
* **Adding Nodes:** Incorporate functions into the state graph using the `.add_node()` method, taking a unique node identifier and the function itself.
* **Standard Edges:** Use `.add_edge()` to define standard directional paths from a starting node to a destination node (e.g., automatically passing updated state from an increment node to a print node).

---

## 4. Conditional Branching and Routing
* **Conditional Edges:** Special edges control next-node processing via functions that evaluate the current state (e.g., checking if counter `n` is greater than or equal to 13).
* **Implementation Methods:**
  * **Dictionary Mapping:** Use `.add_conditional_edges()`, passing the node name, the condition-evaluating function, and a dictionary mapping condition outputs to destination nodes.
  * **Direct Routing:** Alternatively, design the conditional node/function to directly return the next node name based on state evaluation.
* **Entry Points:** Indicate the starting node for state processing using the `.set_entry_point()` method.

---

## 5. Compiling and Invoking the Workflow
* **Compilation:** After connecting all nodes, edges, and entry points, call `.compile()` to build the runnable application object.
* **Execution:** Run the application by calling `.invoke()` with an initial state dictionary (e.g., `{"n": 1, "letter": ""}`).
* **Execution Lifecycle:** 
  1. State is passed to the increment node, processed, and passed to the print node.
  2. The stop condition evaluates whether the threshold (e.g., $n \ge 13$) is met.
  3. If false, execution loops back to the increment node; if true, it routes to the end state, completing the workflow and returning the final state value.
