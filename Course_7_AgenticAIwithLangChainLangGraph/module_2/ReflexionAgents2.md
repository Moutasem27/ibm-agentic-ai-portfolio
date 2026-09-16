# Building Reflexion Agents: Prompt Engineering, Schema Design, and Iterative Feedback Loops


## 1. Environment Setup and Tool Configuration
* **Essential Imports & Setup:** Begin by importing necessary packages and setting up your project environment.
* **Tavily Search Integration:** Configure the Tavily Search tool by providing your API key, creating an instance that returns up to 5 results per query, and testing it with a sample query (e.g., breakfast recipes) to return structured JSON dictionaries containing `title`, `URL`, and `content` keys.
* **Baseline LLM Initialization:** Instantiate a chat model using OpenAI/GPT to generate initial responses to user questions.

---

## 2. Persona Prompt Engineering & Schema Design
* **Persona Configuration:** Use system messages to direct the LLM's behavioral persona (e.g., acting as Dr. Paul Saladino for a controversial carnivore/animal-based nutrition approach, or Dr. Peter Accia for longevity and evidence-based health).
* **Structured Reflection Schemas:** 
  * Define classes such as `Reflection` to capture what information is missing or superfluous.
  * Create an `AnswerQuestion` schema class containing attributes for the answer, reflections, and search queries.
* **Tool Binding:** Bind the LLM to the schema using `.bind_tools()`, causing the LLM to treat the custom data class like a tool call schema.
* **State Tracking:** Utilize a `response_list` variable (analogous to LangGraph state) to append human messages, AI messages, and tool responses across iterations.

---

## 3. Building the Reflexion Graph in LangGraph
* **Message Graph Initialization:** Import necessary graph components, initialize a message graph, and set an iteration limit (e.g., maximum of 4 iterations).
* **Defining Nodes:**
  * **Draft/Respond Node:** Uses the initial chain to process user queries and generate the first structured draft response.
  * **Execute Tools Node:** Extracts search queries from the draft node/revisor and calls the Tavily Search tool, wrapping results in tool messages.
  * **Revisor Node:** Utilizes the `revisor_chain` (guided by expert longevity prompts and extended citation schemas) to refine the response based on search results and critiques.
* **Connecting Edges:**
  * Connect the respond node to tool execution via a standard edge.
  * Connect tool execution to the revisor node.
  * Set the entry point to the draft responder node.
* **Conditional Routing & Compilation:** Add a conditional edge from the revisor via an event loop function (`event_loop`) to evaluate tool message counts, deciding whether to continue iterating or terminate at the end node. Compile the graph into a runnable application.

---

## 4. Evaluation and Results
* **Query Execution:** Running a query like *"I'm pre-diabetic and need to lower my blood sugar, and I have heart issues"* processes through alternating AI and tool messages.
* **Responder vs. Revisor Output:** 
  * *Initial Responder:* Provided a general advocacy for animal-based nutrition (eggs, fatty meats, organ foods), avoiding grains and plant foods without deep individual tailoring.
  * *Final Revisor Iteration:* Improved the output by incorporating scientific citations, measurable physiological outcomes (e.g., postprandial glucose levels), and more precise food guidance.
