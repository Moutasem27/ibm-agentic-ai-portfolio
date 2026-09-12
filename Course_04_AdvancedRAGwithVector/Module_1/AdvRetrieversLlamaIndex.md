# Advanced Retrievers in LlamaIndex: Comprehensive Reference Guide & Master Summary
*A Technical Summary of Index Types, Core Retrievers, and Fusion Strategies*

---

## 1. Core Index Types in LlamaIndex
LlamaIndex provides three primary index types tailored for different data structures and retrieval requirements:
* **VectorStoreIndex:** Stores vector embeddings for each document chunk, making it best suited for semantic retrieval and standard LLM-powered pipelines.
* **DocumentSummaryIndex:** Generates and stores summaries of documents at indexing time, utilizing those summaries to filter documents before retrieving the full content. This is particularly useful for large and diverse document sets that exceed an LLM's context window.
* **KeywordTableIndex:** Extracts keywords from documents and maps them directly to specific content chunks, making it ideal for exact keyword matching and hybrid or rule-based search scenarios.

---

## 2. Core and Advanced Retrievers

### Vector Index Retriever
* Utilizes vector embeddings to find semantically relevant content.
* Ideal for general-purpose search and widely used in RAG (Retrieval-Augmented Generation) pipelines.

### BM25 Retriever (Keyword-Based Search)
* **Foundation (TF-IDF):** Relies on Term Frequency (how often a word appears in a document) and Inverse Document Frequency (how rare a word is across all documents) to highlight words frequent in one document but rare overall.
* **BM25 Improvements:** Improves on TF-IDF by incorporating term frequency saturation to reduce the impact of repeated terms and adjusting for document length. It retrieves content based on exact keyword matches rather than semantic similarity.

### Document Summary Index Retriever
* Uses document summaries instead of raw documents to locate relevant content.
* **Two Operating Versions:**
  1. *LLM-Based:* Uses an LLM to evaluate relevance (more thorough, but time-consuming and expensive).
  2. *Semantic Similarity-Based:* Uses semantic similarity between the query and summary embeddings (more efficient for large collections).
* *Note:* Regardless of the version used, the retriever always returns the **original documents**, not their summaries.

### Auto-Merging Retriever
* Designed to preserve context in long documents using a hierarchical structure (hierarchical chunking into parent and child nodes).
* If a sufficient threshold of child nodes belonging to the same parent is retrieved, the retriever automatically returns the larger **parent node** instead, consolidating related content and preserving broader context.

### Recursive Retriever
* Designed to follow relationships between nodes using explicit references (such as citations in an academic paper or metadata links).
* Supports both chunk references and metadata references, allowing it to traverse and retrieve related content across multiple documents or layers of abstraction.

### Query Fusion Retriever
* Combines results from different retrievers (such as combining vector-based and keyword-based methods).
* Optionally generates multiple variations of a query using an LLM to improve recall and coverage.

---

## 3. Query Fusion & Merging Strategies
The Query Fusion Retriever supports several statistical and algorithmic strategies to merge result sets:
* **Reciprocal Rank Fusion (RRF):** Combines ranked lists by assigning higher scores to documents that appear near the top of any list. It is robust and does not rely on raw score magnitudes.
* **Relative Score Fusion:** Normalizes scores within each result set by dividing by the maximum score, preserving the relative confidence of each individual retriever.
* **Distribution-Based Fusion:** Employs statistical techniques like z-score normalization or percentile ranking to combine results, effectively handling score variability across different models.

---

## 4. Recommended Retrievers by Use Case
* **General Q&A:** Use a `VectorIndexRetriever`, potentially combined with a `BM25 Retriever` to blend semantic relevance with keyword matching.
* **Technical Documents:** Prioritize `BM25` as the primary retriever for exact terms, utilizing the `VectorIndexRetriever` as a secondary retriever to add contextual flexibility.
* **Long Documents:** Use the `Auto-Merging Retriever` to fetch larger parent nodes only when enough shorter child versions are matched.
* **Research Papers:** Use the `Recursive Retriever` to trace and pull relevant content from cited papers and cross-references.
* **Large Document Sets:** Use the `DocumentSummaryIndex Retriever` to narrow down the document pool, followed by vector search within that subset to retrieve the most pertinent content.
