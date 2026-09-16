# Building AI Self-Improvement: Creating Reflection Agents with LangChain & LangGraph

## 1. Overview and Core Concept
* **What are Reflection Agents?** Reflection agents are AI systems designed to analyze their own performance critically and enhance their strategies iteratively, learning from mistakes rather than relying on a single pass.
* **Types of Reflection Agents:**
  * **Basic Reflection Agent:** Generates an output, critiques it, and refines it through a loop.
  * **Reflexion Agent:** Adds external feedback loops and memory.
  * **Language Agent Tree Search (LATS):** Combines reflection with tree-search algorithms for complex decision-making.

---

## 2. The Generator-Reflector Loop in Action
The core mechanism relies on two distinct LLM roles working in tandem:
1. **Generator:** Produces an initial draft or response based on user input.
2. **Reflector:** Evaluates the output, offers constructive critique, and identifies areas for improvement.

### Example Workflow (LinkedIn Post Optimization)
* **Iteration 1:** 
  * *Generator:* Writes an initial post.
  * *Reflector:* Critiques the tone, structure, or content (wrapped as a human message to act as feedback).
* **Iteration 2+:** 
  * *Generator:* Incorporates the critique to produce a refined draft.
  * *Loop Control:* Continues until criteria are met or a maximum iteration limit is reached.

---

## 3. Tech Stack and Architecture

### LangChain for LLM Roles
* **Model:** IBM's Granite model.
* **Prompts:** Uses `ChatPromptTemplate` combined with system messages to strictly define roles (e.g., professional LinkedIn content strategist) and message placeholders for conversation memory.
* **Chaining:** Connects prompts to LLMs using the pipe (`|`) operator.

### LangGraph for Workflow State (`MessageGraph`)
* LangGraph simplifies state management using **`MessageGraph`**, a specialized graph type whose state holds only an array of messages (`HumanMessage`, `AIMessage`, `SystemMessage`).
* Each turn automatically appends messages, maintaining context and memory across iterations.

---

## 4. Step-by-Step Graph Construction

### Step 1: Define Nodes
* **Generate Node:** Takes the state input, passes it to the generation chain, and implicitly updates the state by appending an `AIMessage`.
* **Reflect Node:** Takes message history, passes it to the reflection chain, and wraps the critique inside a **`HumanMessage`** (this ensures the generator treats the critique as incoming user guidance rather than breaking the feedback loop).

### Step 2: Configure Graph Topology
* **Add Nodes:** Register `generate` and `reflect` nodes into the graph.
* **Edges:** Create a one-way connection from `reflect` back to `generate`.
* **Entry Point:** Set the graph entry point to the `generate` node.
* **Router & Conditional Logic:** 
  * Define a router function (e.g., `should_continue`) to check message history length.
  * If messages exceed a threshold (e.g., 6 messages), route to the `END` node. Otherwise, route back to the reflection phase.
* **Compilation:** Compile the workflow using the `compile()` function before execution.

---

## 5. Key Takeaways
* Reflection agents drastically improve output quality by separating creation from critical evaluation.
* LangChain's dynamic chat prompts and message placeholders make it easy to steer multi-role LLMs.
* LangGraph's `MessageGraph` and conditional routing enable seamless, cyclical agentic workflows.
