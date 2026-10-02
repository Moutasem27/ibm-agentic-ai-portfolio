# Comprehensive Study Guide: LLM Workflow Design Patterns in LangGraph

This study guide covers five core design patterns for building agentic AI systems using LangGraph, based on the course transcripts.

---

## Module 1: Fundamental Design Patterns

### 1. Sequential Pattern (Prompt Chaining)
The simplest workflow design where the output of one Large Language Model (LLM) or agent is passed directly as the input to the next.

* **Key Rationale**: Agents perform better on specialized tasks; breaking complex problems into sub-tasks improves overall accuracy and control.
* **Example Use Case**: Resume Summary & Cover Letter Generation.
  * *Node 1*: Technical specialist reads job description $\rightarrow$ outputs key qualification summary.
  * *Node 2*: Professional writer accepts resume summary + job description $\rightarrow$ outputs tailored cover letter.
* **LangGraph State Schema**:
  ```python
  # ChainState Schema
  {
      "job_description": str,
      "resume_summary": str,
      "cover_letter": str
  }
  ```
* **Execution Flow**:
  1. Entry point $\rightarrow$ `generate_resume_summary` node (updates `resume_summary`).
  2. Sequential Edge $\rightarrow$ `generate_cover_letter` node (reads `resume_summary` and `job_description`, updates `cover_letter`).
  3. End point.

---

### 2. Routing Pattern
An intelligent selection mechanism where a central router agent analyzes user input and dynamically selects the appropriate downstream node/agent.

* **Key Rationale**: Optimizes resource usage and agent specialization by directing traffic based on intent classification.
* **Example Use Case**: Query Router (Summarize vs. Translate).
* **Key Implementation Details**:
  * Uses structured output (e.g., Pydantic schema) bound to the router LLM to force output choices: `"summarize"` or `"translate"`.
  * Output is passed to `add_conditional_edges` to direct graph flow.
* **LangGraph State Schema**:
  ```python
  # RouterState Schema
  {
      "user_input": str,
      "task_type": str,  # "summarize" or "translate"
      "output": str
  }
  ```
* **Execution Flow**:
  1. Entry point $\rightarrow$ `router_node` (updates `task_type`).
  2. Conditional Edge (`add_conditional_edges`):
     * If `task_type == "summarize"` $\rightarrow$ `summarize_node`.
     * If `task_type == "translate"` $\rightarrow$ `translate_node`.
  3. Nodes execute and store result in `output` $\rightarrow$ End.

---

### 3. Parallelization Pattern
Executes multiple independent LLM tasks simultaneously rather than sequentially to significantly boost processing speed and throughput.

* **Key Rationale**: Tasks that do not depend on each other can be parallelized. Results are later unified by an aggregator.
* **Example Use Case**: Multi-language Translation Aggregator.
* **LangGraph State Schema**:
  ```python
  # ParallelState Schema
  {
      "text": str,              # Input English text
      "french": str,            # Translation outputs
      "spanish": str,
      "japanese": str,
      "combined_output": str    # Aggregated result
  }
  ```
* **Execution Flow**:
  1. Entry point connects to three parallel edges simultaneously:
     * `french_node`
     * `spanish_node`
     * `japanese_node`
  2. All translation nodes execute concurrently and update their respective state keys.
  3. All three nodes connect to `aggregator_node`, which reads `text`, `french`, `spanish`, and `japanese`, concatenating them into `combined_output`.
  4. `aggregator_node` $\rightarrow$ End.

---

## Module 2: Advanced Design Patterns

### 4. Orchestrator Pattern
Handles dynamic workflows where task complexity and quantity are unknown in advance. An orchestrator breaks down requests and dynamically spawns worker tasks.

* **Key Concept**: Unlike static parallelization, task breakdown is determined **in real-time**.
* **Analogy**: Cruise ship party planner (orchestrator) receiving variable multi-course requests and assigning dynamic chef teams (worker agents).
* **Key Mechanism (`Send` API & Worker State)**:
  * Uses LangGraph's `Send` interface to pass state to worker nodes dynamically.
  * **Shared State vs. Worker State**:
    * *Shared State*: Tracks overall workflow data (e.g., `meals`, `completed_menu`, `final_meal_guide`).
    * *Worker State*: A local container passed to individual workers containing task-specific details (e.g., specific `dish` details).
    * `completed_menu` uses `operator.add` reducer to concurrently aggregate worker outputs without overwriting.

* **State Schemas**:
  ```python
  # Overall State
  {
      "meals": str,
      "sections": List[Dish],       # Dynamic task list from Orchestrator
      "completed_menu": Annotated[List[str], operator.add], # Shared Reducer Key
      "final_meal_guide": str
  }

  # Worker State
  {
      "section": Dish,              # Single task payload
      "completed_menu": List[str]   # Reference to shared reducer key
  }
  ```

* **Execution Flow**:
  1. `orchestrator` receives input (e.g., "Prepare pasta, tacos, curry"), generates structured output (`Dish` objects), and populates `sections`.
  2. `assign_workers` node evaluates `sections` and uses `Send` to dynamically spawn worker nodes (`chef_worker`) for each section in parallel.
  3. Each `chef_worker` generates detailed execution steps and appends the result to `completed_menu`.
  4. `synthesizer` node waits for all dynamic workers, combines `completed_menu` into `final_meal_guide`, and completes workflow.

---

### 5. Evaluator-Optimizer Pattern (Reflection Loop)
An iterative feedback cycle where a **Generator** produces an output and an **Evaluator** assesses it against target criteria. If rejected, constructive feedback is routed back to the generator to refine the result.

* **Key Rationale**: Enables continuous self-correction and quality assurance until explicit quality thresholds or iteration limits are met.
* **Example Use Case**: Multi-Agent Investment Advisory Board.
  * **Personas**:
    * *Kathy Wood*: Generator for initial high-risk/innovative strategy.
    * *Ray Dalio*: Generator for strategy refinement based on feedback.
    * *Warren Buffett*: Value-oriented Evaluator providing risk grading and critical feedback.

* **LangGraph State Schema**:
  ```python
  {
      "investor_profile": str,
      "target_grade": str,       # e.g., "Conservative", "Moderate", "High"
      "current_grade": str,
      "investment_plan": str,
      "feedback": str,
      "iteration_count": int     # Prevents infinite loops
  }
  ```

* **Execution Flow**:
  1. `risk_grading_node`: Analyzes `investor_profile` and assigns `target_grade`.
  2. `generator_node`:
     * If `feedback` is empty $\rightarrow$ uses *Kathy Wood* prompt for initial draft.
     * If `feedback` exists $\rightarrow$ uses *Ray Dalio* prompt to refine plan incorporating *Warren Buffett's* critique.
  3. `evaluator_node` (*Warren Buffett*):
     * Increments `iteration_count`.
     * Evaluates `investment_plan` against `investor_profile`.
     * Returns `current_grade` and structured `feedback`.
  4. `route_investment` (Conditional Edge):
     * If `current_grade == target_grade` OR `iteration_count >= MAX_LIMIT` $\rightarrow$ **ACCEPT** $\rightarrow$ End.
     * Else $\rightarrow$ **REJECT** $\rightarrow$ Loop back to `generator_node` with updated `feedback`.

---

## Pattern Summary Matrix

| Pattern | Input Determinism | Work Execution | Primary Mechanism / Key Features |
| :--- | :--- | :--- | :--- |
| **Sequential** | Static | Serial | Direct step-by-step output passing |
| **Routing** | Static | Single Path | Conditional branching via classifier router |
| **Parallelization** | Static | Concurrent | Fixed multi-node branch execution + Aggregator |
| **Orchestrator** | Dynamic | Dynamic Concurrent | Dynamic task allocation via `Send` & worker state reducers (`operator.add`) |
| **Evaluator-Optimizer** | Dynamic | Iterative Loop | Generator-Evaluator reflection loop with threshold termination |

---

## Self-Assessment Questions

1. **Which LangGraph operator is essential when aggregating parallel worker responses into a single state list without overwriting prior outputs?**
   * *Answer*: `operator.add` used as a state reducer.
2. **What distinguishes the Orchestrator pattern from standard Parallelization?**
   * *Answer*: Standard parallelization has fixed, pre-defined branches. The Orchestrator dynamically determines the number and nature of parallel tasks at runtime based on task complexity.
3. **What safeguards should always be included in an Evaluator-Optimizer loop to avoid infinite execution?**
   * *Answer*: An iteration counter state variable and a maximum iteration threshold check within the routing logic.