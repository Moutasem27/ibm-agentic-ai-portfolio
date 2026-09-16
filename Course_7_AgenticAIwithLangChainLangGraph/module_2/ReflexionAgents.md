# Reflexion Agents: Iterative Self-Critique and Tool-Augmented Learning

## 1. What Are Reflexion Agents?
* **Beyond Standard Reflection:** While basic reflection agents iteratively review and refine outputs, Reflexion agents go further by producing verifiable claims, current real-time information, and formal citations rather than just improved opinions.
* **Post-Training Adaptation:** Reflexion allows AI systems to learn from new information, evaluate past responses, and continuously improve even after the model has completed training.
* **Core Strengths:**
  * **Self-Improvement:** Continually analyze performance, find and fix weaknesses, and adjust reasoning strategies with each iteration.
  * **External Integration:** Call tools like web searches or APIs to fetch real-time data.
  * **Transparency & Justification:** Back up responses with formal citations and clear reasoning derived from reflection cycles.

---

## 2. The Reflexion Workflow & Architecture

### Step 1: The Responder / Generator Role
* **Initial Generation:** A user query (e.g., *"I need more minerals in my diet"*) is processed by a responder LLM guided by a system prompt (e.g., *"You are a fitness coach"*).
* **Structured Outputs:** Instead of returning plain text, the LLM outputs a structured object based on a defined data model or schema containing fields such as `response`, `self-critique`, and `query`. This output becomes an `AIMessage` and is logged alongside `HumanMessage` inputs into a `response_list`.

### Step 2: Tool Integration & Data Retrieval
* **Tool Execution:** External tools extract search queries from the responder's structured output to gather real-time data (including titles, content, and URLs).
* **State Tracking:** Tool call results are appended to the `response_list` to maintain full conversational and operational context across runs.

### Step 3: The Revisor Role
* **Refinement:** The revisor role takes the `response_list` (focusing on the responder's self-critique and tool outputs) and modifies the initial response.
* **Structured Revisor Schema:** Like the generator, the revisor outputs a structured object containing:
  * Revised response text
  * Formal references and integrated citations
  * Updated self-critique
  * The next set of search queries (if further refinement is needed)

---

## 3. Summary of Key Workflow Components

| Component / Role | Primary Responsibility |
| :--- | :--- |
| **Responder (Generator)** | Takes user queries, applies system prompts, and outputs initial structured responses containing answers, critiques, and search queries. |
| **External Tools** | Extracts search queries, fetches real-time data (titles, contents, URLs), and feeds results back into the workflow. |
| **Response List** | A persistent history tracking `HumanMessages`, `AIMessages`, self-critiques, and tool outputs across iterations. |
| **Revisor** | Analyzes prior critiques and tool data to refine responses, integrate formal citations, and generate subsequent queries. |
