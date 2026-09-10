# Essential Database Operations in Chroma DB: Comprehensive Reference Guide
*A Technical Summary of Collection and Document Management*

---

## 1. Managing Collections in Chroma DB
Collections serve as the core organizational units for data in Chroma DB, analogous to tables in relational databases.

* **Creating a Collection:** 
  1. Import `chromadb` and embedding utilities (`embedding_functions`).
  2. Define an embedding model using Chroma's embedding functions (e.g., SentenceTransformers via `SentenceTransformerEmbeddingFunction`).
  3. Initialize the Chroma client (`chromadb.Client()`) and call `.create_collection()` with a logical name and optional metadata dictionary.
* **Connecting to an Existing Collection:** Establish a connection to a pre-existing collection using the `.get_collection()` method.
* **Modifying Collections:** 
  * Use the `.modify()` method to alter collection properties such as its name and metadata key-value pairs.
  * **Important Limitation:** Fundamental properties like the embedding model or distance metric **cannot** be modified on an existing collection. To apply these changes, you must clone the collection, which can be computationally expensive for substantial amounts of stored data.

---

## 2. Core Document Operations

### Adding Documents (`.add`)
Inserts documents into a collection using Python lists:
* `documents`: List of text strings.
* `metadatas`: List of dictionaries containing custom key-value pairs (e.g., document source and version information).
* `ids`: Unique string identifier required for every document passed to the `ids` parameter.

### Retrieving Documents (`.get`)
* Calling `.get()` without arguments returns all documents in the collection as a Python dictionary.
* **Embeddings Visibility:** Embeddings are stored internally within the collection but are hidden by default to keep output clean. To view them, explicitly pass `include=['embeddings']`.
* **Targeted Retrieval:** Retrieve individual records by passing their specific IDs to the `.get()` method.

### Updating Documents (`.update`)
* Modifies existing records based on their unique IDs (e.g., specifying `'id1'`).
* Chroma DB automatically handles re-embedding in the background, re-computing vectors as soon as the update is submitted.

### Deleting Documents (`.delete`)
* Remove documents by passing a list of target IDs, applying a `where` metadata filter, or using a combination of both IDs and filters.

---

## 3. Configuring Distance Metrics via HNSW
Chroma DB utilizes the Hierarchical Navigable Small World (HNSW) algorithm to perform approximate nearest neighbor searches. The distance function in the embedding space is controlled by the `space` parameter within the HNSW configuration dictionary at creation time:

* **`l2` (Default):** Squared L2 norm (Euclidean distance).
* **`cosine`:** Cosine distance.
* **`ip`:** Inner product (dot product) distance.
