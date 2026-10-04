# Comprehensive Study Guide: BeeAI & AG2 (AutoGen) Frameworks

---

## Part 1: BeeAI Framework

### 1. Framework Overview & Core Philosophy
* **Origin & Governance:** Developed under the **Linux Foundation AI & Data Program** and backed by **IBM Research**.
* **Target:** Enterprise-grade, production-ready platform designed for building scalable AI agents and multi-agent systems.
* **Dual-Language Parity:** Complete feature and API parity between **Python** and **TypeScript**.
* **Provider-Agnostic Backend:** Native integration with 10+ LLM providers (e.g., IBM watsonx.ai/Granite, OpenAI, Anthropic, Grok, Ollama).

#### Core Architectural Advantages
1. **Production-Ready Infrastructure:** Built-in caching, memory optimization, resource management, and OpenTelemetry monitoring.
2. **Advanced Agentic Patterns:** Out-of-the-box support for ReAct (Reasoning and Acting), systematic thinking, and dynamic multi-agent coordination.
3. **Standards Compliance:** Interoperable with open standards including the **Model Context Protocol (MCP)** and **Agent-to-Agent (A2A)** protocol.
4. **Observability:** Complete end-to-end tracing and monitoring via OpenTelemetry integration.

---

### 2. The Async/Await Programming Model
* **Why BeeAI Uses Async/Await:** Modern agentic workflows involve heavy I/O operations (streaming LLM tokens, external web searching, multi-agent RPCs, API calls). Python’s asynchronous event loop allows execution without blocking application threads.
* **Key Mechanics:**
  * `async def`: Defines an asynchronous coroutine function.
  * `await`: Suspends coroutine execution until the async promise completes, enabling non-blocking execution across parallel tasks.

---

### 3. Core Building Blocks & Syntax Examples

#### A. Necessary Imports
```python
import asyncio
from pydantic import BaseModel, Field
from typing import List

from beeai_framework.backend import ChatModel, ChatModelParameters, SystemMessage, UserMessage
from beeai_framework.agents.experimental import RequirementAgent
from beeai_framework.memory import UnconstrainedMemory
from beeai_framework.tools.search.wikipedia import WikipediaTool
from beeai_framework.tools.think import ThinkTool
from beeai_framework.tools.handoff import HandoffTool
from beeai_framework.tools import Tool
from beeai_framework.agents.experimental.requirements.conditional import ConditionalRequirement
from beeai_framework.agents.experimental.requirements.ask_permission import AskPermissionRequirement
```

#### B. Basic Model Invocation
```python
# Initialize model
llm = ChatModel.from_name(
    "watsonx:ibm/granite-4-h-small", 
    ChatModelParameters(temperature=0)
)

# Define messages
messages = [
    SystemMessage(content="You are a helpful AI assistant."),
    UserMessage(content="Explain machine learning in simple terms.")
]

# Run asynchronously
async def main():
    response = await llm.create(messages=messages)
    print(response.get_text_content())

asyncio.run(main())
```

#### C. Dynamic Prompt Templates (`SimplePrompt`)
* Dynamic templates utilize mustache-style syntax (`{{variable}}`) to ensure consistent, non-biased prompt formatting across repeating tasks.

```python
# Dynamic template workflow:
# 1. Instantiate template string with placeholders
# 2. Render template with input dictionary
# 3. Pass rendered output as a UserMessage
```

#### D. Structured Outputs with Pydantic
Eliminates text-parsing fragility by forcing the LLM to return validated, typed Python data structures via `llm.create_structure()`.

```python
class BusinessPlan(BaseModel):
    business_name: str = Field(description="Catchy name for the business")
    elevator_pitch: str = Field(description="30-second description")
    revenue_streams: List[str] = Field(description="Ways to make money")

async def main():
    llm = ChatModel.from_name("openai:gpt-5-nano", ChatModelParameters(temperature=0))
    response = await llm.create_structure(
        schema=BusinessPlan,
        messages=[
            SystemMessage("You are a business consultant."),
            UserMessage("Create a business plan for a food delivery app.")
        ]
    )
    # Returns typed BusinessPlan object directly
    print(response.object.business_name)

asyncio.run(main())
```

#### E. Conversational Memory (`UnconstrainedMemory`)
`UnconstrainedMemory` stores the complete history of messages without truncating context.

* `await memory.add(message)`: Adds a single message asynchronously.
* `await memory.add_many([msg1, msg2])`: Performs bulk message addition.
* `memory.is_empty()`: Returns boolean status of memory content.
* `memory.messages`: Accesses array of stored message objects.
* `memory.reset()`: Purges complete conversation context.

---

### 4. Advanced BeeAI Agents & Control Systems

#### RequirementAgent
`RequirementAgent` combines model execution, persistent context memory, external tools, and declarative rule enforcement.

```python
agent = RequirementAgent(
    llm=llm,
    memory=UnconstrainedMemory(),
    instructions="You are an AI assistant specialized in data analysis."
)

async def main():
    result = await agent.run("What is machine learning?")
    print(f"Answer: {result.answer.text}")

asyncio.run(main())
```

#### Tool Integration & Requirements Summary

| Requirement / Component | Purpose / Functionality |
| :--- | :--- |
| **`ConditionalRequirement`** | Controls execution ordering, limits frequency (e.g., `max_invocations=1`). |
| **`AskPermissionRequirement`** | Forces human approval before executing sensitive tool calls. |
| **`ThinkTool`** | Prompts internal agent reasoning/reflection prior to final output creation. |
| **`force_at_step`** | Guarantees a tool runs on a specific execution step index. |
| **`force_after`** | Forces tool execution sequentially following another tool (e.g., forcing reflection after tool use). |
| **`consecutive_allowed`** | Disallows executing the same tool back-to-back. |
| **`GlobalTrajectoryMiddleware`** | Tracks execution trajectories for debugging and audit logging. |

#### ReAct Agent Configuration Pattern
```python
agent = RequirementAgent(
    llm=llm, 
    memory=UnconstrainedMemory(),
    instructions="You are a helpful assistant.",
    tools=[ThinkTool(), WikipediaTool()],
    requirements=[
        ConditionalRequirement(
            ThinkTool,
            force_at_step=1,           # Begin with internal thinking
            force_after=Tool,          # Force thinking after any tool call
            consecutive_allowed=False, # Prevent repeating back-to-back thinking
            max_invocations=3          # Bound overall reasoning cycles
        )
    ]
)
```

#### Human-In-The-Loop Security
```python
agent = RequirementAgent(
    llm=llm,
    memory=UnconstrainedMemory(),
    instructions="You are a research assistant.",
    tools=[WikipediaTool(), ThinkTool()],
    requirements=[
        AskPermissionRequirement(WikipediaTool), # Asks user permission before web search
        ConditionalRequirement(ThinkTool, force_at_step=1, max_invocations=3)
    ]
)
```

#### Custom Tool Creation Procedure
1. Create a parameter schema class inheriting from `pydantic.BaseModel`.
2. Define a tool class inheriting from `beeai_framework.tools.Tool`.
3. Implement core tool logic in the async `_run()` method.

---

## Part 2: AG2 Framework (Formerly AutoGen)

### 1. Framework Overview & Architecture
* **Definition:** Open-source framework designed for building multi-agent AI applications via structured interactions and specialized role assignment.
* **Philosophy:** Complex problem solving is best achieved by specialized agent roles (e.g., researcher, coder, reviewer) collaborating together rather than using one massive monolithic prompt.
* **Model Support:** Provider-agnostic via `LLMConfig` (supports OpenAI, Anthropic, etc.).

---

### 2. Key AG2 Imports & Setup
```python
# Installation
# pip install ag2[openai]

from autogen import (
    ConversableAgent, 
    AssistantAgent, 
    UserProxyAgent, 
    GroupChat, 
    GroupChatManager,
    register_function
)
from autogen.coding import LocalCommandLineCodeExecutor
from autogen.llm_config import LLMConfig

# Global Model Configuration
llm_config = LLMConfig(api_type="openai", model="gpt-5-nano")
```

---

### 3. Core AG2 Conversation Patterns

#### A. Two-Agent Conversation
The fundamental pattern involving two `ConversableAgent` instances communicating directly.

```python
with llm_config:
    student = ConversableAgent(
        name="student",
        system_message="You are a curious student who asks clear questions.",
        human_input_mode="NEVER"
    )
    
    tutor = ConversableAgent(
        name="tutor",
        system_message="You are a helpful tutor with clear explanations.", 
        human_input_mode="NEVER"
    )

chat_result = student.initiate_chat(
    recipient=tutor,
    message="Can you explain what a neural network is?",
    max_turns=2,
    summary_method="reflection_with_llm"
)

print("Final Summary:", chat_result.summary)
```

#### B. Safe Code Generation & Execution
Splits reasoning (`AssistantAgent`) and execution (`UserProxyAgent`) for safe sandboxed automation.

```python
assistant = AssistantAgent(
    name="assistant",
    system_message="Helpful assistant who writes clear Python code."
)

user_proxy = UserProxyAgent(
    name="user_proxy",
    human_input_mode="NEVER",
    max_consecutive_auto_reply=5,
    code_execution_config={
        "executor": LocalCommandLineCodeExecutor(work_dir="coding")
    }
)

user_proxy.initiate_chat(
    recipient=assistant,
    message="Plot a sine wave using matplotlib and save as sine_wave.png"
)
```

#### C. Group Chat Orchestration
Coordinates multiple specialized agents using a `GroupChatManager`.

* **Speaker Selection Methods:**
  * `auto`: LLM selects the next speaker based on context.
  * `round_robin`: Cycles sequentially through agents.
  * `manual`: Human selects the next speaker manually.
  * `random`: Selects speaker randomly.

```python
lesson_planner = ConversableAgent(
    name="planner_agent",
    system_message="Create lesson plans for 4th graders.",
    description="Makes lesson plans"
)

lesson_reviewer = ConversableAgent(
    name="reviewer_agent",
    system_message="Review plans and suggest up to 3 brief edits.",
    description="Reviews lesson plans and suggests edits"
)

teacher = ConversableAgent(
    name="teacher_agent", 
    system_message="Suggest topics and reply DONE when satisfied.",
    is_termination_msg=lambda x: "DONE" in x.get("content", "").upper()
)

groupchat = GroupChat(
    agents=[teacher, lesson_planner, lesson_reviewer],
    speaker_selection_method="auto"
)

manager = GroupChatManager(
    name="group_manager",
    groupchat=groupchat,
    llm_config=llm_config
)

teacher.initiate_chat(
    recipient=manager,
    message="Make a simple lesson about the moon.",
    max_turns=6,
    summary_method="reflection_with_llm"
)
```

---

### 4. AG2 Human Oversight & Tools

#### Human Input Modes (`human_input_mode`)
* `"ALWAYS"`: Pauses execution to require human verification/input at every iteration.
* `"NEVER"`: Fully autonomous agent execution.
* `"TERMINATE"`: Prompts for human input only when termination conditions are met.

#### Function / Tool Registration
```python
def is_prime(n: int) -> str:
    """Check if a number is prime."""
    if n < 2: return "No"
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0: return "No"
    return "Yes"

register_function(
    is_prime,
    caller=math_asker,      # Agent requesting tool execution
    executor=math_checker,  # Agent running the code execution
    description="Check if a number is prime. Returns Yes or No."
)
```

#### Enforcing Structured Output in AG2
```python
from pydantic import BaseModel

class TicketSummary(BaseModel):
    customer_name: str
    issue_type: str
    urgency_level: str
    recommended_action: str

llm_config = LLMConfig(
    api_type="openai",
    model="gpt-5-nano",
    response_format=TicketSummary
)
```

---

## Part 3: Framework Comparison & Production Best Practices

### Decision Matrix

| Evaluation Factor | Choose **BeeAI** | Choose **AG2 (AutoGen)** |
| :--- | :--- | :--- |
| **Primary Goal** | Production-grade software with strict governance. | Rapid prototyping, multi-agent collaboration research. |
| **Governance & Control** | Granular execution rules (`Requirements`). | Configurable agent loops and speaker roles. |
| **Enterprise Features** | OpenTelemetry monitoring, native MCP / A2A support. | Sandboxed command execution, local execution wrappers. |
| **Language Support** | Complete Python & TypeScript parity. | Primarily Python-focused ecosystem. |
| **Multi-Agent Pattern** | Modular `HandoffTool` delegation topology. | Flexible `GroupChat`, `Sequential`, and `Nested` chats. |

---

### Essential Production Best Practices

#### 1. Security & Credentials
* **Secrets Management:** Never hardcode API credentials. Read keys strictly from environment variables or dedicated secret stores.
* **Code Sandboxing:** Execute code using isolated sandbox container directories rather than native local directories.

#### 2. Loop Prevention & Token Cost Control
* Always set explicit termination rules (`max_turns`, `max_consecutive_auto_reply`, or string termination checks like `"DONE"` or `"TERMINATE"`).

#### 3. Determinism & Reliability
* Set `temperature=0.0` when executing tool calls, database operations, and structured output formatting.
* Configure fallback model chains inside `llm_config` to handle provider outages seamlessly.

#### 4. Design Modularity
* Keep tool definitions modular with single-purpose functionality.
* Version Pydantic schemas over time to preserve backward compatibility as software requirements evolve.