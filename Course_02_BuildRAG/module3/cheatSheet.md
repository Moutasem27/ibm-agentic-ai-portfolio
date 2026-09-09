# Cheat Sheet: Building RAG Apps with LlamaIndex
*Core Concepts, Components, and Architecture Summary*

---

## 1. Overview & Framework Comparison
LlamaIndex is a leading framework for LLM-powered context augmentation, widely used for building chatbots, RAG applications, and specialized AI assistants. 

* **LlamaIndex Strengths:** Simplicity, ease of development, and powerful native tools with sensible defaults.
* **LangChain Comparison:** Frequently compared to LangChain, which features more external integrations, a highly modular design, and more granular customization of individual components.

---

## 2. Core Data Abstractions: Documents & Nodes
* **Documents:** LlamaIndex's `Document` class wraps and stores entire documents from a document store. Beyond raw text, it holds vector embeddings, file metadata (creation date, source directory), and relational links to other documents.
* **Loading Documents:** Documents are loaded using tools like `SimpleDocumentReader` (supporting individual files or entire directories) or via an extensive registry of connectors at **LlamaHub.ai**.
* **Nodes:** Documents are broken down into smaller chunks called **Nodes**. Similar to Documents, a LlamaIndex Node preserves metadata, relationships, and vector embeddings.
* **Text Splitters:** Includes native splitters like `SentenceSplitter` (which recursively splits text based on a list of characters while enforcing a maximum token length) as well as the `LangChainNodeParser` wrapper, allowing developers to utilize any text splitter from LangChain directly within LlamaIndex.

---

## 3. Embedding Generation & Vector Storage
Unlike frameworks that separate embedding generation from database insertion, LlamaIndex handles both in a **single step**:
* **VectorStoreIndex:** The core class used to generate embeddings and store nodes. By default, it stores nodes in-memory, but it can easily wrap external vector databases such as **ChromaDB, FAISS, or Milvus**.

---

## 4. Prompt Embedding & Retrieval
* **Unified Retrieval:** Embedding a user's prompt and retrieving relevant chunks typically occurs in one step by creating a retriever from a `VectorStoreIndex` instance via the `.as_retriever()` method.
* **Model Consistency:** Using a retriever generated directly from the vector store index guarantees that the exact same embedding model used for the document nodes is applied to the user's prompt.
* **Advanced Options:** LlamaIndex provides a wide array of advanced retrievers for complex retrieval patterns.

---

## 5. Prompt Augmentation, Response Synthesis, & Query Engines
LlamaIndex streamlines the final phases of RAG by combining steps into unified objects:
* **Response Synthesizer:** If retrieved nodes are already available, a response synthesizer takes the user's original prompt and retrieved nodes as inputs, performs prompt augmentation, queries the LLM, and outputs the final response.
* **Query Engines:** Eliminates the need to handle retrieved nodes as an intermediary step. By calling `.as_query_engine()` on a vector store index instance, the query engine takes the user's raw prompt as input and outputs the LLM's final response entirely under the hood.
* **Prompt Templates:** LlamaIndex controls prompt augmentation using customizable prompt templates—predefined structures containing placeholders for the user's original query and retrieved text chunks.
