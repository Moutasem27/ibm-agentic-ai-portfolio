# LangChain Expression Language (LCEL) Reference Guide

## Learning Objectives
By the end of this reading, you will be able to:
- Understand LCEL's key capabilities, benefits, and architectural patterns for assembling modular chains[cite: 1].
- Utilize standardized Runnable components and their core methods (`invoke`, `batch`, `stream`)[cite: 1].
- Apply LCEL functions for basic operations, composition, data manipulation, and advanced error handling[cite: 1].
- Select the appropriate orchestration approach among direct calls, LCEL, and LangGraph based on workflow complexity[cite: 1].

---

## 1. LCEL Capabilities & Benefits
LangChain Expression Language (LCEL) is a declarative method for assembling chains from modular components within the LangChain framework[cite: 1]. Rather than prescribing step-by-step instructions, LCEL allows you to specify the outcome, enabling LangChain to optimize its code[cite: 1].

* **Run optimized parallel execution:** Reduces latency and increases overall application performance by running components concurrently[cite: 1].
* **Use guaranteed async support:** Enables smooth, non-blocking workflows that improve responsiveness and throughput in complex chains[cite: 1].
* **Stream outputs incrementally:** Delivers immediate feedback to users, lowers perceived latency, and monitors progress through each stage of the pipeline[cite: 1].
* **Trace all steps automatically with LangSmith:** Provides visibility into your chain's behavior to quickly debug, monitor performance, and improve reliability[cite: 1].
* **Call all chains through a shared LCEL API:** Simplifies integration and enables consistent behavior across workflows[cite: 1].
* **Deploy chains with LangServe:** Accelerates the transition from development to production with minimal overhead[cite: 1].
* **Uses concise and expressive syntax:** Eases connectivity among components for building robust data pipelines[cite: 1].

---

## 2. Standardized Runnables
Each component conforms to the LCEL standardized **Runnable** interface, representing any component that can transform input into output[cite: 1]. All components implement a standard set of methods (`invoke()`, `batch()`, `stream()`), making them interoperable[cite: 1].

| Runnable Type | Description | Example Use Case |
| :--- | :--- | :--- |
| **ChatModel**[cite: 1] | Interfaces with LLM APIs for chat[cite: 1] | Generate conversational responses[cite: 1] |
| **PromptTemplate**[cite: 1] | Creates formatted prompts from variables[cite: 1] | Prepare structured inputs for LLMs[cite: 1] |
| **OutputParser**[cite: 1] | Converts raw outputs to structured data[cite: 1] | Extract structured data from LLM responses[cite: 1] |
| **RunnableLambda**[cite: 1] | Wraps custom Python functions[cite: 1] | Implement custom business logic[cite: 1] |
| **RunnableSequence**[cite: 1] | Chains multiple Runnables together[cite: 1] | Create multi-step processing pipelines[cite: 1] |

---

## 3. Runnable Chains: Use Cases and Workflows
You can build runnable chains by connecting components from the standardized Runnable interface, where each component passes its output directly to the next[cite: 1].

| Use Case | Components | Workflow Summary |
| :--- | :--- | :--- |
| **Simple question answering**[cite: 1] | `PromptTemplate` → `ChatModel` → `StrOutputParser`[cite: 1] | Format a question, send to LLM, return text response[cite: 1] |
| **Retrieval augmented generation (RAG)**[cite: 1] | `Retriever` → `PromptTemplate` → `ChatModel` → `OutputParser`[cite: 1] | Find relevant documents, combine with prompt, generate response[cite: 1] |
| **Function calling**[cite: 1] | `PromptTemplate` → `ChatModel` → `Tool`[cite: 1] | Format prompt, generate function call, parse function parameters, execute function[cite: 1] |
| **Structured output**[cite: 1] | `PromptTemplate` → `ChatModel` → `JsonOutputParser`[cite: 1] | Format prompt, generate response, parse to JSON object[cite: 1] |

---

## 4. LCEL Function Reference

### Basic Operations
* **`invoke()` / `ainvoke()`**: Execute a Runnable with a single input (process one input and get one output)[cite: 1].
* **`batch()` / `abatch()`**: Process multiple inputs efficiently in parallel (run the same operation on multiple inputs at once)[cite: 1].
* **`stream()` / `astream()`**: Return incremental results as they're generated (show partial responses as they're created)[cite: 1].

### Composition Patterns
* **Pipe operator `\|` or `.pipe()`**: Create a sequence of Runnables where the output of one becomes the input to the next[cite: 1].
* **`RunnableParallel`**: Execute multiple Runnables with the same input concurrently (process the same input in different ways simultaneously)[cite: 1].
* **`RunnableLambda`**: Convert Python functions into Runnables to add custom logic within a chain[cite: 1].

### Data Manipulation Patterns
* **`RunnablePassthrough.assign()`**: Add new fields to the input dictionary (augment input with additional data while preserving the original input)[cite: 1].
* **`RunnablePassthrough()`**: Return input unchanged (include the original input as part of the output)[cite: 1].
* **`.pick()`**: Select specific keys from the dictionary output (filter output to only needed fields)[cite: 1].

### Advanced Patterns
* **`.bind()`**: Set default values for parameters (fix certain parameters while leaving others configurable)[cite: 1].
* **`.with_fallbacks()`**: Try alternative Runnables if the primary fails (handle errors by providing backup components)[cite: 1].
* **`.with_retry()`**: Add automatic retry capability on failure (e.g., handling network issues)[cite: 1].

### Configuration
* **`config` parameter**: Control runtime execution by passing settings like concurrency limits and tracing parameters into `.invoke()`, `.batch()`, or `.stream()` methods[cite: 1].
* **`.with_config()`**: Create a Runnable with a default configuration applied to all invocations automatically[cite: 1].

### Streaming and Batching
* **`astream_events()`**: Provide a detailed stream of execution events to monitor the entire process, including intermediate steps[cite: 1].
* **`batch_as_completed()` / `abatch_as_completed()`**: Process inputs in parallel and return results as soon as they are available[cite: 1].

---

## 5. Orchestration Guidelines: When to Use What
Choose your orchestration method based on the complexity of your application:

| Use Case | When to Use | Recommended Tool |
| :--- | :--- | :--- |
| **Single LLM call**[cite: 1] | You need to generate text from a prompt; overhead of setting up a chain is not justified[cite: 1] | LLM directly[cite: 1] |
| **Simple chains**[cite: 1] | Straightforward pipeline (e.g., prompt + LLM + parser + basic retrieval); benefits from parallelization, streaming, and linear flow with minimal branching[cite: 1] | LCEL[cite: 1] |
| **Complex logic with branching**[cite: 1] | Requires complex state management, conditional flows, loops, cycles, or multi-agent system interactions[cite: 1] | LangGraph[cite: 1] |
