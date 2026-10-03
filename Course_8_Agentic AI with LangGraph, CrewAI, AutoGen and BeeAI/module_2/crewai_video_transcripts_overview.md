# CrewAI Video Transcripts & Workflows Guide

This document combines structured notes, key concepts, and summaries from the three CrewAI video transcripts.

---

## Transcript 1: Designing AI Agent Workflows with CrewAI

### Overview & Core Concepts
CrewAI is designed for multi-agent collaboration using clearly defined roles and tasks. It revolves around four primary building blocks:

1. **Task:** Defines what needs to be accomplished (akin to a director setting vision).
2. **Agent:** An LLM-powered entity assigned a role, goal, and backstory (akin to an actor with skills).
3. **Tool:** Functional components (APIs, search engines, custom functions) used by agents or tasks to perform specific actions.
4. **Flow / Process:** Rules that define how tasks run and how agents interact (e.g., Sequential or Hierarchical).

---

### Agent & Task Structure

#### Defining an Agent
Agents use structured prompts to build personality, context, and domain expertise:
* **Role:** The expert persona (e.g., *Senior Research Analyst*).
* **Goal:** The driving objective behind the agent's decisions (e.g., *Uncover cutting-edge insights*).
* **Backstory:** Contextual behavior and identity (e.g., *An expert analyst who translates data into actionable findings*).
* **LLM & Settings:** Shared LLM instance (e.g., Llama on Watsonx), `verbose=True` for debugging, `allow_delegation=False` if managing only assigned tasks.

#### Defining a Task
* **Description:** Detailed instructions on what the agent should do (e.g., *Analyze the latest generative AI breakthroughs*).
* **Expected Output:** Defines success and formatting (e.g., *A detailed, insight-rich summary*).
* **Agent:** The specific agent assigned to execute the task.

---

### Execution & Outputs
* **Sequential Process:** Tasks run linearly (`Process.sequential`). The output of Task 1 automatically feeds into Task 2 as context.
* **Crew Object:** The central orchestrator uniting `agents`, `tasks`, `process`, and shared `llm`.
* **Kickoff Method:** Invokes the workflow (`crew.kickoff(inputs={'topic': 'Generative AI breakthroughs'})`).
* **Crew Output Object:**
  * `result.raw`: Unified final textual output from the workflow.
  * `tasks_output`: Detailed breakdown of individual task outputs.
  * `token_usage`: Metrics tracking prompt, completion, and total tokens for performance/cost analysis.

---

## Transcript 2: Structured Outputs, YAML, and CrewBase Classes

### Overview
This transcript demonstrates a 5-agent Meal Planning system incorporating structured data models, modular configuration using YAML, and `@CrewBase` container classes.

---

### Structured Outputs with Pydantic
Using Pydantic models ensures consistent, typed, and validated outputs exchanged between agents without raw text parsing errors.

#### Example Models
1. **`GroceryItem` (Base Unit):**
   * Fields: `name`, `quantity`, `estimated_price`, `store_category`.
2. **`MealPlan`:**
   * Fields: `meal_name`, `cooking_difficulty`, `servings`, `ingredients` (List of `GroceryItem`).
3. **`ShoppingCategory`:**
   * Fields: `section_name`, `items` (List of `GroceryItem`), `total_estimated_cost`.
4. **`GroceryShoppingPlan` (Composite Model):**
   * Combines `MealPlan`, `ShoppingCategory`, `total_budget`, and `shopping_tips`.

#### Task Integration
Assigning Pydantic models to task definitions enforces formatted returns:
```python
task = Task(
    description="...",
    expected_output="...",
    agent=shopping_organizer,
    output_pydantic=GroceryShoppingPlan, # Enforces Pydantic schema
    output_file="shopping_list.json"      # Saves structured output
)
```

---

### Modular Setup: YAML & `@CrewBase`
Instead of defining all configuration in pure Python code, YAML allows separating agent/task configurations from execution logic.

#### Configuration (`agents.yaml` / `tasks.yaml`)
Define role, goal, backstory, description, and expected output inside YAML files.

#### Loading via `@CrewBase`
```python
from crewai.project import CrewBase, agent, task, crew

@CrewBase
class LeftoversCrew():
    """Leftover management crew container"""

    @agent
    def leftover_manager(self) -> Agent:
        return Agent(
            config=self.agents_config['leftover_manager'],
            verbose=True
        )

    @task
    def leftover_task(self) -> Task:
        return Task(
            config=self.tasks_config['leftover_task']
        )
```
`@CrewBase` automatically locates the project's config directory and loads parameters without manual file parsing.

---

## Transcript 3: Extending CrewAI with Custom Functions & Tools

### Overview
Custom tools enhance agent capabilities for domain-specific tasks. Tools can be attached via two patterns: **Agent-Centric** and **Task-Centric**.

---

### Creating Custom Tools
Use the `@tool` decorator from `crewai.tools` (or LangChain) to wrap standard Python functions:

```python
from crewai.tools import tool

@tool("Add Numbers")
def add_numbers(numbers: str) -> int:
    """Extracts and calculates the sum of numbers from an input string."""
    # Custom python logic
    return sum_result
```

---

### Tool Assignment Patterns

| Feature | Agent-Centric Approach | Task-Centric Approach |
| :--- | :--- | :--- |
| **Tool Placement** | Assigned directly to `Agent(tools=[...])`. | Assigned to `Task(tools=[...])`. |
| **Agent Decision** | Autonomous: Agent inspects incoming query and selects the appropriate tool. | Guided: Agent is forced to use the specific tool designated for that task step. |
| **Use Case** | Dynamic QA, general-purpose assistants, open-ended reasoning. | Strict multi-step pipelines, audited operational workflows, fixed procedures. |

#### Agent-Centric Example
An *Inquiry Specialist* agent equipped with both `PDFSearchTool` (for FAQs) and `SerperDevTool` (for real-time web search) dynamically evaluates customer queries and picks the appropriate resource.

#### Task-Centric Example
1. **Task 1 (FAQ Search):** Equipped with `PDFSearchTool` -> Agent executes document lookup.
2. **Task 2 (Response Drafting):** No search tools attached -> Agent takes output from Task 1 and formats a polite reply.

---

## Summary Comparison Table

| Concept | Purpose | Key Attributes / APIs |
| :--- | :--- | :--- |
| **Core Architecture** | Multi-agent collaboration & task management | `Agent`, `Task`, `Crew`, `Process` |
| **Data Validation** | Schema enforcement & clear data contracts | Pydantic (`BaseModel`), `output_pydantic`, `output_json` |
| **Configuration** | Clean separation of config from Python code | YAML files, `@CrewBase`, `@agent`, `@task` |
| **Custom Extensibility** | Custom logic execution within agent runs | `@tool` decorator, `Agent-Centric` vs `Task-Centric` assignment |