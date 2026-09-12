# Cheat Sheet: Advanced Retrievers for RAG — Comprehensive Reference Guide
*Based on course material by Wojciech "Victor" Fulmyk (Skills Network)*

---

## 1. Core Retrieval Concepts & Advanced Objectives
Advanced retrievers go beyond simple vector similarity search to provide nuanced, context-aware information retrieval through:
* **Semantic Understanding:** Using embeddings for meaning and context[cite: 4].
* **Keyword Matching:** Precise term-based search for exact specifications[cite: 4].
* **Hierarchical Context:** Maintaining relationships between information levels[cite: 4].
* **Multi-Query Processing:** Generating and combining results from multiple query variations[cite: 4].
* **Fusion Techniques:** Intelligently combining results from different retrieval methods[cite: 4].

### Maximum Marginal Relevance (MMR)
* **Purpose:** Balance relevance and diversity of retrieved results[cite: 4].
* **Method:** Selects documents that are highly relevant to the query **AND** minimally similar to previously selected documents[cite: 4].
* **Benefit:** Avoids redundancy and ensures comprehensive coverage of different query aspects[cite: 4].

---

## 2. LlamaIndex Architecture

### Core Index Types in LlamaIndex
* **VectorStoreIndex:** Stores vector embeddings for each document chunk; best suited for semantic retrieval based on meaning and commonly used in LLM pipelines and RAG applications[cite: 4].
* **DocumentSummaryIndex:** Generates and stores summaries of documents at indexing time to find and retrieve relevant documents[cite: 4]. Ideal for large documents whose meanings would be lost by chunking or that exceed LLM/embedding model context windows[cite: 4]. *Key Point:* It uses summaries for retrieval but returns the **original documents**, not their summaries[cite: 4].
* **KeywordTableIndex:** Extracts keywords from documents and maps them to content chunks for exact keyword matching in rule-based or hybrid search scenarios[cite: 4].

### LlamaIndex Retriever Types
1. **Vector Index Retriever:** The most common retriever; embeds queries and compares them with document embeddings using cosine similarity for general-purpose search and RAG pipelines[cite: 4]. *Limitation:* May miss exact keyword matches when specific terms are crucial[cite: 4].
2. **BM25 Retriever:** Advanced keyword-based retrieval improving on TF-IDF ($TF \times IDF$) by introducing **Term Frequency Saturation** (controlled by $k_1 \approx 1.2$) and **Document Length Normalization** ($b = 0.75$) to prevent long-document bias[cite: 4]. Ideal for technical and legal documentation[cite: 4].
3. **Document Summary Index Retrievers:** Available in two variants:
   * *DocumentSummaryIndexLLMRetriever:* Uses an LLM to analyze queries against summaries (intelligent but expensive)[cite: 4].
   * *DocumentSummaryIndexEmbeddingRetriever:* Uses semantic similarity between queries and summary embeddings (faster and cost-effective)[cite: 4].
   * Both utilize a two-stage approach using summaries to filter documents before returning full document content[cite: 4].
4. **Auto Merging Retriever:** Preserves context in long documents using hierarchical chunking (parent and child nodes)[cite: 4]. If enough child nodes from the same parent are retrieved, it returns the parent node instead, providing dual storage (small chunks for matching, parent chunks for context)[cite: 4].
5. **Recursive Retriever:** Follows relationships and references (such as citations in academic papers or metadata links) between nodes across different layers of abstraction[cite: 4].
6. **Query Fusion Retriever:** Combines results from different retrievers (vector and keyword) and optionally generates query variations using an LLM, supporting three fusion modes:
   * *Reciprocal Rank Fusion (RRF):* The most robust method combining ranked lists using the reciprocal of ranks ($RRF\_score(d) = \sum \frac{1}{rank_i(d) + k}$ where $k=60$); ideal as a default choice for production systems[cite: 4].
   * *Relative Score Fusion:* Preserves score magnitudes while normalizing across query variations (normalized score = original score / max score)[cite: 4].
   * *Distribution-Based Score Fusion:* Uses statistical properties of score distributions such as z-score normalization or percentile ranking for complex queries[cite: 4].

---

## 3. LangChain Architecture

### LangChain Retriever Interface
* **Definition:** An interface that returns documents based on an unstructured query[cite: 4]. 
* More general than a vector store; accepts a string query as input and returns a list of documents or chunks as output without necessarily storing documents itself[cite: 4].

### LangChain Retriever Types
1. **Vector Store-Backed Retriever:** A lightweight wrapper around a vector store class supporting Simple Similarity Search (ranked by similarity, default 4 results), MMR Search (balancing relevance and diversity), and Similarity Score Threshold (returning only documents above a specified threshold)[cite: 4].
2. **Multi-Query Retriever:** Addresses distance-based retrieval variations by using an LLM to generate multiple queries from different perspectives, retrieving a set of documents for each, and taking the unique union of results to yield a larger set of potentially relevant documents[cite: 4].
3. **Self-Querying Retriever:** Converts a natural language query into a structured query with two components: a semantic lookup string and an accompanying metadata filter[cite: 4]. Requires documents to have rich, structured metadata with field descriptions (e.g., *“I want to watch a movie rated higher than 8.5”*)[cite: 4].
4. **Parent Document Retriever:** Solves conflicting chunking desires (small documents for accurate embeddings vs. large documents for context) by storing small chunks for embeddings in the vector store and large parent documents in a document store, fetching small chunks during retrieval and returning their parent documents[cite: 4].

---

## 4. Decision Framework: LlamaIndex vs. LangChain

| Need | LlamaIndex Choice | LangChain Choice |
| :--- | :--- | :--- |
| **Exact keyword matching** | BM25 Retriever[cite: 4] | Vector Store-Backed + custom keyword logic[cite: 4] |
| **Multi-query with fusion** | Query Fusion Retriever (RRF/Relative/Distribution)[cite: 4] | Multi-Query Retriever (union approach)[cite: 4] |
| **Citation following** | Recursive Retriever[cite: 4] | Not directly supported[cite: 4] |
| **Hierarchical context** | Auto Merging Retriever[cite: 4] | Parent Document Retriever[cite: 4] |
| **Simple semantic search** | Vector Index Retriever[cite: 4] | Vector Store-Backed Retriever[cite: 4] |
```[cite: 4]
