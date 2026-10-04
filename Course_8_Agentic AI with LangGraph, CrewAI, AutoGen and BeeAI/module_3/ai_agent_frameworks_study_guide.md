# Comprehensive Study Guide: BeeAI & AG2 Frameworks

---

## Part 1: BeeAI Framework

### 1. Overview & Key Capabilities
* **Origin & Governance:** Developed under the **Linux Foundation AI and Data Program** and backed by **IBM Research**.
* **Target:** Built specifically for production-ready, enterprise-grade AI agents and multi-agent systems.
* **Dual-Language Parity:** Complete feature parity across both **Python** and **TypeScript**.
* **Provider-Agnostic Backend:** Native support for 10+ LLM providers (e.g., IBM WatsonX.ai/Granite, OpenAI, Anthropic, Grok, Ollama).

#### Key Architectural Advantages
1. **Production-Ready Architecture:** Built-in caching, memory optimization, resource management, and OpenTelemetry integration.
2. **Advanced Agent Patterns:** Built-in support for ReAct (Reasoning and Acting), systematic thinking, and multi-agent coordination.
3. **Standards Compliance:** Supports protocols like **MCP** (Model Context Protocol) and **A2A** (Agent-to-Agent).
4. **Observability:** Native OpenTelemetry support to monitor, debug, and trace execution in production.

---

### 2. Async/Await Programming Model
* **Rationale:** Non-blocking I/O operations (streaming response chunks, network calls to LLMs, external tool invocations, and multi-agent coordination).
* **Syntax Mechanics:**
  * `async def`: Defines a coroutine function.
  * `await`: Pauses execution of the coroutine until the pending promise/future completes without blocking the event loop.

---

### 3. Core Building Blocks & Syntax

#### A. Basic Conversation Setup
* **Messages:**
  * `SystemMessage`: Defines AI persona and instructions.
  * `UserMessage`: Represents user prompt/input.
* **Execution:** Model invocations are processed asynchronously via `await llm.create(...)`.

#### B. Dynamic Prompt Templates
* **Syntax:** Mustache-style templates (`SimplePrompt`).
* **Purpose:** Ensures structured, consistent prompt generation to reduce variable bias across requests.
* **Usage Workflow:**
  1. Instantiate `SimplePrompt` with placeholdered text.
  2. Invoke `template.render(data_dict)`.
  3. Wrap output inside a `UserMessage`.

#### C. Structured Outputs
* **Mechanism:** Leverages **Pydantic** models.
* **Execution Method:** Uses `create_structure()` instead of standard `create()`.
* **Benefit:** Guarantees typed, validated Python objects returned directly from the LLM, eliminating fragile manual JSON string parsing.

#### D. Conversational Memory
* **`UnconstrainedMemory`:** Simplest memory class; stores complete conversation history without message/token truncations.
* **Key Operations:**
  * `await memory.add(message)`: Adds a single message asynchronously.
  * `await memory.add_many([msg1, msg2])`: Efficient batch addition.
  * `memory.is_empty()`: Checks if messages exist.
  * `memory.messages`: Accesses stored message collection.
  * `memory.reset()`: Purges memory history.

---

### 4. Advanced BeeAI Features & Execution Control

#### RequirementAgent & Tool Integration
`RequirementAgent` extends basic chat models by attaching persistent memory, external tools, and strict runtime behavioral constraints.

* **`ThinkTool`:** Enables internal reasoning/reflection steps prior to generating final user outputs.
* **`HandoffTool`:** Enables delegation of tasks between agents in multi-agent hierarchies.

#### BeeAI Requirements System
Provides fine-grained rule-based control over agent execution loops:

| Requirement / Component | Description / Functionality |
| :--- | :--- |
| **`ConditionalRequirement`** | Restricts tool execution frequency or order (e.g., `max_invocations=1`). |
| **`AskPermissionRequirement`** | Enables **Human-In-The-Loop (HITL)** by pausing execution for explicit human authorization before running a tool. |
| **`ControlTrajectoryMiddleware`** / **`GlobalTrajectoryMiddleware`** | Tracks execution flow and records complete execution logs for debugging. |
| **`force_at_step`** | Forces specific tool usage at a given step (e.g., `force_at_step=1` forces initial thinking). |
| **`force_after`** | Enforces tool sequence dependencies (e.g., forcing `ThinkTool` usage after any tool execution). |
| **`consecutive_allowed`** | Boolean flag controlling whether a tool can execute repeatedly back-to-back. |

#### Building a ReAct Pattern Cycle in BeeAI
A ReAct (Reasoning + Acting) loop is structured using requirements:
1. `force_at_step=1` (force `ThinkTool` at start).
2. `force_after=Tool` (force `ThinkTool` following any tool usage).
3. `consecutive_allowed=False` (prevents loop lock-up on `ThinkTool`).
4. `maxInvocations` set on thinking passes.

#### Custom Tool Creation
1. Inherit input schema from Pydantic `BaseModel` using `Field` descriptions.
2. Inherit tool class from `Tool` base class specifying `name`, `description`, and `input_schema`.
3. Implement custom business logic inside the `_run` method.

---

## Part 2: AG2 Framework (formerly AutoGen)

### 1. Overview & Fundamental Philosophy
* **Core Paradigm:** Multi-agent collaboration using role-based specialization rather than relying on a single monolithic prompt/model.
* **Model Agnostic:** Supports OpenAI, Anthropic, and other major LLM providers via `LLMConfig`.
* **Architecture:** Inherently event-driven, conversable architecture supporting HITL and secure code execution sandboxes.

---

### 2. Primary Agent Types

#### A. `ConversableAgent`
* Base class for all AG2 agents.
* Capable of sending, receiving, replying, and maintaining conversation state.
* Configured via `system_message`, `llm_config`, `human_input_mode`, and `max_consecutive_auto_reply`.

#### B. Specialized Built-in Agents
* **`AssistantAgent`:** Specialized for problem solving, reasoning, and generating code solutions.
* **`UserProxyAgent`:** Represents the user or an automated executor. Can execute code in local/sandboxed command-line environments and route feedback to `AssistantAgent`.

---

### 3. Orchestration & Conversation Patterns

#### Conversation Execution Parameters
* `initiate_chat()`: Starts agent interaction.
* `max_turns`: Sets a hard limit on conversation turns to prevent infinite execution loops and manage token consumption.
* `summary_method="reflection_with_llm"`: Generates an LLM-synthesized summary at conversation termination.

#### Multi-Agent Orchestration Topologies
1. **Two-Agent Chat:** Direct back-and-forth between two specialized roles.
2. **Sequential Chat:** Pipeline flow where agent outputs pass step-by-step through successive refinement stages.
3. **Nested Chat:** Wraps complex sub-conversations as a single operational task node inside a larger workflow.
4. **Group Chat (`GroupChat` & `GroupChatManager`):** Multi-agent discussion orchestrated via a manager.

#### Speaker Selection Strategies in Group Chat
* **`Auto`:** The LLM dynamically chooses the next speaker based on context and capability.
* **`Round_Robin`:** Agents speak in a fixed, sequential sequence.
* **`Manual`:** Human user explicitly selects the next speaker at each step.
* **`Random`:** Randomly selects the next speaker.

---

### 4. Human-In-The-Loop (HITL) Controls
Configured via `human_input_mode`:
* **`ALWAYS`:** Pauses for human input at every single iteration.
* **`NEVER`:** Fully autonomous execution.
* **`TERMINATE`:** Asks for human input only when a termination condition is triggered or the task completes.

---

### 5. Tools & Structured Outputs in AG2

#### Tool Registration
Functions are converted into agent-accessible capabilities using `register_function()`. This explicitly separates **reasoning** (e.g., `AssistantAgent`) from **execution** (e.g., `UserProxyAgent`).

#### Structured Outputs
* Defined using **Pydantic** schema models.
* Configured directly inside `llm_config` using `response_format`.
* Guarantees strict type safety and schema validation for API integration and downstream automation.

---

## Part 3: Production Best Practices Comparison

| Domain | Best Practice Rules |
| :--- | :--- |
| **Security** | Never hardcode API keys; use environment variables/secrets managers. Run code execution exclusively inside sandboxed directories. |
| **Reliability** | Configure `config_list` fallback models to maintain uptime during provider outages. |
| **Model Tuning** | Use `temperature=0.0` for structured outputs/analytical tasks; use `0.7–1.0` for creative tasks. |
| **Loop Prevention** | Enforce `max_turns` and `max_consecutive_auto_reply`. Set explicit termination conditions (e.g., checking for specific string signals like `"TERMINATE"` or `"done"`). |
| **Tool Design** | Keep tools modular with single, focused responsibilities. Implement internal validation, error handling, and complete docstrings. |
| **Schema Design** | Version Pydantic schemas over time to preserve backward compatibility across production software updates. |
