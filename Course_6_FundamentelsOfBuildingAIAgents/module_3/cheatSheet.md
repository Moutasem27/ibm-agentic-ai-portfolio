# LangChain Built-In Agents and Core Agent Types Reference Guide

## I. Understanding Built-in Agents
LangChain provides built-in agents that enable LLMs to make decisions, use tools, access memory, and interact with external systems. These agents are typically accessed in two ways:
* **Prebuilt agent creators:** Utility functions that help quickly set up ready-to-use agents for common use cases (for example, SQL, CSV, Pandas).
* **LangGraph ReActive:** Graph-structured agents with explicit reasoning steps and control flow logic.

---

## II. Exploring Core Agent Types
These agent types define the basic way an agent thinks, chooses tools, and performs actions. They are foundational and can be used to build more specialized agents.

| Agent Type | Description |
| :--- | :--- |
| **ZERO_SHOT_REACT_DESCRIPTION** | Performs reasoning before acting. Picks the right tool using only its description. |
| **REACT_DOCSTORE** | Zero-shot agent with access to a document store for retrieving info (for example, Wikipedia). |
| **SELF_ASK_WITH_SEARCH** | Breaks complex questions into simpler ones and answers using a search tool. |
| **CONVERSATIONAL_REACT_DESCRIPTION** | Maintains conversation history while reasoning and acting. |
| **CHAT_ZERO_SHOT_REACT_DESCRIPTION** | Like a zero-shot agent but optimized for chat models like GPT-4. |
| **CHAT_CONVERSATIONAL_REACT_DESCRIPTION** | Chat-based version of conversational ReAct agent. |
| **STRUCTURED_CHAT_ZERO_SHOT_REACT_DESCRIPTION** | Optimized for chat models and structured tools with multiple inputs. |
| **OPENAI_FUNCTIONS** | Designed to work with OpenAI's function calling schema. |
| **OPENAI_MULTI_FUNCTIONS** | Handles multiple tools using OpenAI's multi-function architecture. |

---

## III. Model Compatibility
Some LLMs may not fully support structured output parsing required by certain agents (for example, `structured-chat-zero-shot-react-description`). If the tool returns a dictionary or complex output, these models might fail with parsing or validation errors.

* **Description:** You can create an agent using `initialize_agent`, where you pass the agent type, so it behaves according to the agent type. The agent in this code will perform reasoning before acting.
* **Code Implementation:**
```python
from langchain.agents import initialize_agent, AgentType

# Example initialization performing reasoning before acting
agent = initialize_agent(
    tools=tools_list,
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True
)
