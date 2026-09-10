# Introduction to Vector Databases and Chroma DB: Master Cheat Sheet & Reference Guide
*Based on course material by Wojciech "Victor" Fulmyk (Skills Network)*

---

## 1. Distance and Similarity Metrics

### L2 Distance (Euclidean Distance)
* **Definition:** The straight-line distance between two vectors $a$ and $b$ in Euclidean space, calculated as the square root of the sum of squared differences between corresponding elements:
  $$L2(a,b) = \sqrt{\sum_{i=1}^{n}(a_i - b_i)^2}$$
[cite: 2]
* **Properties & Use Cases:** Sensitive to both magnitude and direction[cite: 2]. Commonly used in spatial or geometric data, image analysis, computer vision, and geographic mapping to find closest points in 2D/3D space[cite: 2].

### Dot Product (Inner Product)
* **Definition:** Calculated as the sum of the products of corresponding elements:
  $$a \cdot b = \sum_{i=1}^{n} a_i b_i$$
[cite: 2]
* **Alternative Calculation:** Can also be computed using magnitudes and angles:
  $$a \cdot b = \Vert{}a\Vert{} \Vert{}b\Vert{} \cos(\alpha)$$
  where $\Vert{}a\Vert{} = \sqrt{\sum_{k=1}^{n} a_k^2}$ is the L2 norm and $\alpha$ is the angle between $a$ and $b$[cite: 2].
* **Properties & Use Cases:** Sensitive to magnitude and direction[cite: 2]. Larger values indicate higher similarity[cite: 2]. Frequently used in neural network activations and recommender systems where vector length reflects relevance, confidence, or popularity[cite: 2].

### Cosine Similarity and Distance
* **Definition:** Measures the cosine of the angle between two vectors, focusing strictly on orientation rather than magnitude:
  $$\text{cosine\_similarity}(a, b) = \frac{a \cdot b}{\Vert{}a\Vert{} \Vert{}b\Vert{}}$$
[cite: 2]
* **Distance Conversion:** $\text{cosine\_distance}(a, b) = 1 - \text{cosine\_similarity}(a, b)$[cite: 2].
* **Normalized Calculation:** Cosine similarity equals the dot product of normalized vectors ($\text{norm}(a) \cdot \text{norm}(b)$), where normalization is achieved by dividing a vector by its L2 norm ($\text{norm}(a) = \frac{a}{\Vert{}a\Vert{}}$) so that squared components sum to 1[cite: 2].
* **Properties & Use Cases:** Invariant to vector length[cite: 2]. Ideal for high-dimensional, sparse data such as text embeddings and document similarity in natural language processing (NLP)[cite: 2].

### Metric Selection Summary
| Metric | Sensitive to Magnitude? | Normalized? | Best Suited For |
| :--- | :--- | :--- | :--- |
| **L2 Distance** | Yes ($\checkmark$)[cite: 2] | No ($\times$)[cite: 2] | Spatial data, clustering[cite: 2] |
| **Cosine Distance** | No ($\times$)[cite: 2] | Yes ($\checkmark$)[cite: 2] | Text, embeddings, NLP[cite: 2] |
| **Dot Product** | Yes ($\checkmark$)[cite: 2] | No ($\times$)[cite: 2] | Neural networks, recommender systems[cite: 2] |

---

## 2. Vector Databases vs. Traditional Databases

| Function | Traditional Databases | Vector Databases |
| :--- | :--- | :--- |
| **Data Representation** | Structured format using tables, rows, columns[cite: 2] | Multi-dimensional vectors encoding complex/unstructured data[cite: 2] |
| **Data Search** | SQL queries for structured data[cite: 2] | Similarity searches for vectorized data[cite: 2] |
| **Indexing** | B-trees for efficient retrieval[cite: 2] | Specialized indices such as the graph-based HNSW for approximate nearest neighbor search[cite: 2] |
| **Scalability** | Resource augmentation or data sharding[cite: 2] | Distributed architectures for horizontal scaling[cite: 2] |
| **Applications** | Business applications, transactional systems[cite: 2] | Context-aware AI applications, similarity search, NLP, multimedia analysis[cite: 2] |

* **Vector Libraries vs. Vector Databases:** Vector libraries are in-memory tools providing basic similarity capabilities, whereas vector databases offer full CRUD (Create, Read, Update, Delete) operations and enterprise production deployments[cite: 2].

---

## 3. Vector Indexes & The HNSW Algorithm

* **Vector Index Purpose:** Brute-force comparison across all vectors is inefficient at scale[cite: 2]. Vector indexes organize high-dimensional embeddings to reflect vector space geometry, allowing search algorithms to prune large dataset portions and maintain low-latency performance for millions or billions of vectors[cite: 2].
* **HNSW (Hierarchical Navigable Small World):** A fast, scalable graph-based vector index designed for approximate nearest neighbor (ANN) search in high-dimensional spaces; it is the sole indexing method supported by Chroma DB[cite: 2].
* **How HNSW Works:** Builds a multi-layered graph where upper layers contain sparse overviews for fast navigation and the bottom layer holds all vectors[cite: 2]. Each vector connects to nearby neighbors in a "small world" network structure[cite: 2].
* **HNSW Distance Metric Parameters (`space`):** 
  * `l2`: Squared L2 Euclidean distance (default)[cite: 2].
  * `ip`: Inner (dot) product distance[cite: 2].
  * `cosine`: Cosine distance[cite: 2].
* **HNSW Performance Trade-offs:**
  * Higher `ef_search`: Improves accuracy/recall, but slows down query performance[cite: 2].
  * Higher `ef_construction`: Enhances index quality and accuracy, but increases build time[cite: 2].
  * Higher `max_neighbors`: Improves search performance, but increases memory usage[cite: 2].

---

## 4. Chroma DB Setup, Configuration, and Data Operations

### Basic Setup & Collection Creation
```python
import chromadb
from chromadb.utils import embedding_functions

# Define embedding function
ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="all-MiniLM-L6-v2"
)

# Create client
client = chromadb.Client()

# Collection Creation with HNSW Configuration
collection = client.create_collection(
    name="my_collection_name",
    metadata={"topic": "query testing"},
    configuration={
        "hnsw": {
            "space": "cosine",
            "ef_search": 100,
            "ef_construction": 100,
            "max_neighbors": 16
        },
        "embedding_function": ef
    }
)
```[cite: 1, 2]

### Adding Documents
```python
collection.add(
    documents=[
        "Document text 1",
        "Document text 2"
    ],
    metadatas=[
        {"source": "source1", "category": "type1"},
        {"source": "source2", "category": "type2"}
    ],
    ids=["id1", "id2"]
)
```[cite: 2]

### Retrieving Documents
```python
# Get all documents
all_items = collection.get()

# Get with metadata filter
filtered_items = collection.get(
    where={"source": "source1"}
)
```[cite: 2]

---

## 5. Filtering in Chroma DB

Chroma DB supports a dual-filtering approach combining structured metadata attributes and unstructured document content searches (full-text search)[cite: 2].

### Metadata Filtering Operators (`where`)
* Basic Equality: `where={"key": "value"}` or `where={"key": {"$eq": "value"}}`[cite: 2]
* Comparison Operators: `"$ne"` (not equal), `"$gt"` (greater than), `"$gte"` (greater than or equal), `"$lt"` (less than), `"$lte"` (less than or equal)[cite: 2]
* List Operators: `"$in"` (in list), `"$nin"` (not in list)[cite: 2]
* Logical Operators: Combine conditions using `"$and"` and `"$or"`[cite: 2]

**Example with Logical Operators:**
```python
collection.get(
    where={
        "$and": [
            {"source": {"$eq": "langchain.com"}},
            {"version": {"$lt": 0.3}}
        ]
    }
)
```[cite: 2]

### Document Content Filtering (`where_document`)
Searches text content directly using `"$contains"` or `"$not_contains"` (note: document filtering is case-sensitive)[cite: 2].
```python
# Contains text
where_document={"$contains": "pandas"}

# Does not contain text
where_document={"$not_contains": "library"}

# Combined with logical operators
where_document={
    "$or": [
        {"$contains": "LangChain"},
        {"$contains": "Python"}
    ]
}
```[cite: 2]

---

## 6. Similarity Search Execution

### Basic and Filtered Queries
```python
# Basic Query
results = collection.query(
    query_texts=["search term"],
    n_results=3
)

# Query with Metadata Filter
results = collection.query(
    query_texts=["polar bear"],
    n_results=1,
    where={"topic": "animals"}
)

# Query with Document Filter
results = collection.query(
    query_texts=["polar bear"],
    n_results=1,
    where_document={"$not_contains": "library"}
)

# Combined Filters Query
results = collection.query(
    query_texts=["polar bear"],
    n_results=1,
    where={"topic": "animals"},
    where_document={"$not_contains": "library"}
)
```[cite: 2]

---

## 7. Common End-to-End Workflow Pattern
```python
import chromadb
from chromadb.utils import embedding_functions

# 1. Setup embedding function and client
ef = embedding_functions.SentenceTransformerEmbeddingFunction(
    model_name="all-MiniLM-L6-v2"
)
client = chromadb.Client()

# 2. Create collection with configuration
collection = client.create_collection(
    name="collection_name",
    configuration={
        "hnsw": {"space": "cosine"},
        "embedding_function": ef
    }
)

# 3. Add documents
collection.add(
    documents=documents_texts, 
    metadatas=metadata, 
    ids=ids
)

# 4. Perform similarity search
results = collection.query(
    query_texts=["query"], 
    n_results=5
)

# 5. Process results
for i, (doc_id, score, text) in enumerate(
    zip(results['ids'][0], results['distances'][0], results['documents'][0])
):
    print(f"Rank {i+1}: ID: {doc_id}, Score: {score:.4f}, Text: {text}")
```[cite: 2]
