# Vector Databases for Recommendation Systems and RAG: Master Cheat Sheet & Reference Guide
*Based on course material by Wojciech "Victor" Fulmyk (Skills Network)*

---

## 1. Overview of RAG
* **Definition:** RAG is a framework that enhances language models by retrieving relevant information from external sources to generate more accurate, grounded responses while reducing potential hallucinations[cite: 3].
* **Core Problems Solved:** 
  * LLMs have limited context windows, making it impossible to include all information in a single prompt[cite: 3].
  * Knowledge is frozen at the point of training[cite: 3].
  * LLMs are prone to hallucinating facts[cite: 3].

---

## 2. The RAG Pipeline Steps
1. **Document Preparation:** Relevant source documents are provided and split into smaller chunks[cite: 3].
2. **Embedding:** Source documents or their individual chunks are converted into numerical vector embeddings[cite: 3].
3. **Storage:** Sources and their embeddings are stored in a vector database such as Chroma DB[cite: 3].
4. **Prompt Reception:** The user's prompt is received by the system[cite: 3].
5. **Prompt Embedding:** The user's prompt is converted into an embedding[cite: 3].
6. **Retrieval:** A retriever selects the chunks from the vector store that best match the user's prompt[cite: 3].
7. **Prompt Augmentation:** The retrieved text is combined with the user's original prompt to produce an augmented prompt[cite: 3].
8. **Response Generation:** The augmented prompt is passed to the LLM to produce a context-aware response[cite: 3].

---

## 3. Vector Database Responsibilities in RAG
Vector databases can manage several core responsibilities within a RAG pipeline[cite: 3]:
* Embedding both source documents and user prompts[cite: 3].
* Storing those vector embeddings[cite: 3].
* Retrieving the most relevant matches[cite: 3].
* Supplying retrieved content for prompt augmentation[cite: 3].
*(Note: Steps like document and prompt embedding can also be performed externally, in which case the vector database acts primarily as a storage and retrieval layer)[cite: 3].*

### Why Use Vector Databases for RAG Steps?
1. **Prevents Critical Mistakes:** Avoids errors like using mismatched embedding models for documents and queries or mislinking embeddings to their source documents[cite: 3].
2. **Faster & Cleaner Development:** Reduces custom logic and moving parts, keeping the codebase simpler and easier to maintain, implement, and debug[cite: 3].
3. **High Performance:** Built specifically for high-speed, scalable semantic searches using advanced indexing algorithms that custom-built alternatives struggle to match[cite: 3].

---

## 4. Common RAG Pipeline Pitfalls & Solutions
* **Mismatched Embedding Models:** Using different models for documents and queries can break retrieval entirely[cite: 3]. *Solution:* Use the same embedding model throughout (typically automated by vector databases)[cite: 3].
* **Poor Chunking Strategy:** Creating chunks that are either too large or too small hurts performance[cite: 3]. *Solution:* Choose a chunk size long enough to preserve meaning without crowding in irrelevant content[cite: 3].
* **Neglecting Re-embedding:** Failing to re-embed content after changing the distance metric or embedding model[cite: 3]. *Note:* In databases like Chroma DB, this cannot be done directly on an existing collection and may require cloning the collection[cite: 3].
* **Uncritical Acceptance:** Assuming retrieved results are automatically the best answer[cite: 3]. *Solution:* Always test and tune results, as minor adjustments make a significant difference[cite: 3].

---

## 5. Chroma DB Practical Operations & Code Reference

### Creating Collections
```python
import chromadb
import chromadb.utils.embedding_functions as embedding_functions

# Define the embedding model
sentence_transformer_ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="all-MiniLM-L6-v2"
)

# Define chromadb client
client = chromadb.Client()

# Create collection
collection = client.create_collection(
    name="my_collection",
    metadata={"description": "A collection for storing user data"},
    configuration={
        "embedding_function": sentence_transformer_ef
    }
)
```[cite: 3]

### Connecting to Existing Collections
```python
# Connect to existing collection
collection = client.get_collection(name="my_collection")
```[cite: 3]

### Modifying Collections
```python
# Alter collection using modify method
collection.modify(
    name="new_collection_name",
    metadata={"key": "value"}
)
```[cite: 3]
*Important:* Changing fundamental properties like the embedding model or distance metric cannot be done on an existing collection and requires cloning the collection[cite: 3].

### Adding Documents
```python
# Add documents to collection
collection.add(
    documents=[
        "This is a document about LangChain",
        "This is a document about LlamaIndex"
    ],
    metadatas=[
        {"source": "langchain.com", "version": "0.2"},
        {"source": "llamaindex.ai", "version": "0.12"}
    ],
    ids=["id1", "id2"]
)
```[cite: 3]

### Retrieving Documents
```python
# Get all documents (returns Python dictionary)
results = collection.get()

# Get specific documents by ID
results = collection.get(ids=["id1"])

# Include embeddings in results
results = collection.get(include=['embeddings'])
```[cite: 3]

### Updating Documents
```python
# Update existing documents
collection.update(
    ids=["id1"],
    metadatas=[{"source": "langchain.com", "version": "0.3"}],
    documents=["This an updated document about LangChain"]
)
```[cite: 3]
*Note:* Chroma DB automatically handles background re-computing of embeddings when updates are submitted[cite: 3].

### Deleting Documents
```python
# Delete by IDs
collection.delete(ids=["id1"])

# Delete using metadata filter
collection.delete(where={"source": "doc_to_delete.pdf"})

# Combine IDs and filters
collection.delete(
    ids=["id1"],
    where={"version": "1.0"}
)
```[cite: 3]

### Configuring Distance Functions (HNSW)
Chroma DB uses the Hierarchical Navigable Small World (HNSW) algorithm for approximate nearest neighbor searches, configured via the `space` parameter[cite: 3]:
* `l2` (default) — squared L2 norm[cite: 3]
* `cosine` — cosine distance[cite: 3]
* `ip` — inner product or dot product distance[cite: 3]

```python
# Specify distance function at collection creation
collection = client.create_collection(
    name="my_collection",
    metadata={"description": "A collection for storing user data"},
    configuration={
        "embedding_function": sentence_transformer_ef,
        "hnsw": {"space": "cosine"}
    }
)
```[cite: 3]

---

## 6. Division of Labor: What Vector Databases Don't Handle
Certain tasks are executed outside the database layer[cite: 3]:
* **Chunking** is typically completed before data enters the vector database[cite: 3].
* **Extra retrieval logic** such as filtering and re-ranking may require supplemental tools[cite: 3].
* **Prompt augmentation** is handled outside the database[cite: 3].
* **LLM integration** is not built directly into most vector databases[cite: 3].

### RAG Frameworks
Tools like **LangChain** and **LlamaIndex** wrap around your vector database to fill these gaps by providing structure, connecting all components, and simplifying the development and deployment of RAG applications[cite: 3].
