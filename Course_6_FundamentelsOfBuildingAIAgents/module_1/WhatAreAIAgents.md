# Understanding AI Agents and Compound AI Systems
---

## 1. From Monolithic Models to Compound AI Systems
- **Limitations of Standalone Models:** Models trained on their own are constrained by their training data, limiting what they know and what tasks they can solve, and they are difficult and resource-intensive to adapt[cite: 1].
- **The Compound AI System Approach:** Recognizes that complex problems are better solved using system design principles rather than relying solely on a single model[cite: 1]. 
- **System Characteristics:** 
  - Modular structure combining models (LLMs, tuned models, image generation) with programmatic components (output verifiers, query breakdown logic, database searches, and tools)[cite: 1].
  - Faster and easier to adapt than tuning a model[cite: 1].
  - Traditional Retrieval-Augmented Generation (RAG) is a common example of a compound AI system with fixed, human-defined programmatic control logic (meaning its execution path is rigid and will fail if given an out-of-scope query like asking about the weather instead of database records)[cite: 1].

---

## 2. What are AI Agents?
- An agentic approach puts a **large language model in charge of the control logic** instead of relying on a rigid, human-defined program path[cite: 1].
- Made possible by improvements in LLM reasoning capabilities, allowing systems to "think slow"—taking a complex problem, breaking it down, creating a plan, executing step-by-step, evaluating where they get stuck, and readjusting[cite: 1].

### Core Capabilities of LLM Agents:
1. **Reasoning:** Putting the model at the core of problem-solving to plan and reason through each step[cite: 1].
2. **Action (Tools):** Using external programs (APIs, web search, calculators, code execution, other models) that the model can choose when and how to call[cite: 1].
3. **Memory:** Storing inner logs (thought processes) and conversation history to personalize experiences and reference past interactions[cite: 1].

---

## 3. The ReAct Framework
- **ReAct** combines the **Reasoning** and **Acting** components of LLM agents[cite: 1].
- **Workflow:**
  1. A user query is fed into an LLM prompted to plan rather than give an immediate response[cite: 1].
  2. The LLM decides when to act by invoking external tools[cite: 1].
  3. It observes the output or errors returned by the tool, determines whether it answers the query, and iterates on the plan until reaching a final answer[cite: 1].

---

## 4. Programmatic vs. Agentic Approach (Choosing the Right Architecture)
- **Programmatic Approach (Narrow Problems):** Best for narrow, well-defined problems where queries follow a consistent path. More efficient because an agentic approach would introduce unnecessary looping and iteration[cite: 1].
- **Agentic Approach (Complex Tasks):** Best for complex, open-ended tasks (like solving independent GitHub issues across a spectrum of queries) where configuring every single path manually would require excessive effort[cite: 1].
- Most agentic systems incorporate a **human-in-the-loop** as accuracy continues to improve[cite: 1].
