# Llama Index
It is a framework for building LLM-powered context augmentation
* Context augmentation: is the process of making data available to the LLM and allows LLM to perform a specific task while grounding the LLM's response in the provided context.
## Typical use cases
* Question-Answering with RAG
* Chatbots extend the basic RAG pipeline
* Document understanding and data extraction

## Code block
from llama_index.core import Document

mydocument = Document(text="Hello LlamaIndex")

mydocument.dict()

# From Vector Stores to Query Engines
*A Comprehensive Transcription & Technical Summary*

---

## 1. Objectives & High-Level RAG Review
This module covers fundamental and advanced concepts in LlamaIndex, including:
* Embedding generation and vector store management.
* Retrieval mechanisms in the presence of vector stores.
* Prompt augmentation and LLM response generation.
* Query engines that combine multiple RAG steps into a single unified object.

### The RAG Pipeline Review
In Retrieval-Augmented Generation (RAG):
1. **Ingestion:** Documents are loaded, chunked into smaller nodes, and embedded into vectors.
2. **Storage:** The resulting vectors are stored in a vector store.
3. **Retrieval:** A user prompt is embedded, and similar chunks from the source documents are retrieved from the vector store.
4. **Generation:** The prompt and retrieved text chunks are combined and sent to a Large Language Model (LLM) to generate a context-aware response.

---

## 2. Embedding & Vector Storage in LlamaIndex
LlamaIndex manages embedding generation and storage through the `VectorStoreIndex` class.

### Simple In-Memory Use Cases
For basic applications, embeddings can be generated using a default model and stored entirely in-memory by passing the document nodes as the sole parameter to `VectorStoreIndex`.

### Complex Use Cases (Custom Models & Persistent Storage)
For advanced applications requiring a custom embedding model and persistent storage:
1. **Import Libraries:** Import your vector database (e.g., ChromaDB) and the function to import embedding models (e.g., from HuggingFace).
2. **Define Embedding Model:** Instantiate your chosen custom embedding model.
3. **Setup Storage Context:** Configure your vector store (like ChromaDB) and define a storage context.
4. **Initialize Index:** Pass the nodes, embedding model, and storage context into `VectorStoreIndex` to generate custom vectors and store them in persistent storage.

---

## 3. Retrieval Process in LlamaIndex
Given an initialized `VectorStoreIndex`, retrieval is handled via a retriever object:
* **Creating the Retriever:** Call the `as_retriever()` method on the `VectorStoreIndex` object.
* **Executing Retrieval:** Pass the user's prompt to the retriever object's `retrieve()` method to obtain relevant results.
* **Controlling Results (`similarity_top_k`):** Specify the maximum number of items retrieved by setting the `similarity_top_k` parameter (e.g., setting it to 5 nodes). The retriever returns a ranked list with the most similar nodes at the top.

---

## 4. Prompt Augmentation, LLM Querying, & Response Synthesis
LlamaIndex automates prompt augmentation and querying using specialized components:

* **Response Synthesizer:** Combines prompt augmentation, LLM querying, and response generation. Given a user's prompt and retrieved nodes, the synthesizer's `synthesize()` method generates a response from the LLM. Background steps (like prompt embedding and augmentation) happen automatically without manual intervention.
* **Query Engines (The Ultimate Simplification):** A query engine merges prompt embedding, retrieval, prompt augmentation, LLM querying, and response generation into a single step. Given a user's prompt, the query engine's `query()` method generates the final LLM response, drastically reducing the amount of boilerplate code required to build a RAG application.

---

## 5. Customizing LlamaIndex Applications
LlamaIndex query engines and RAG pipelines can be customized by:
* Changing the default LLM to an alternative model of your choice.
* Defining custom prompt templates for prompt augmentation.
* Specifying a custom retriever to fit specialized application requirements.

---

## Summary of Key Takeaways
* **VectorStoreIndex:** Handles embedding generation and vector storage, supporting both in-memory and persistent storage contexts (e.g., ChromaDB).
* **Retrievers:** Created via `as_retriever()`, allowing control over retrieved node counts using `similarity_top_k`.
* **Response Synthesizer:** Consolidates prompt augmentation, LLM querying, and response generation into one cohesive step.
* **Query Engines:** Condense the entire end-to-end RAG workflow into simple commands while remaining fully customizable.
