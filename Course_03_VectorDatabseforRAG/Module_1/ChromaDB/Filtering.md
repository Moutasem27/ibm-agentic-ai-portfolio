# Filtering in Chroma DB: Metadata and Document Content Search
*A Comprehensive Technical Summary & Reference Guide*

---

## 1. Overview: Chroma DB Filtering vs. SQL
Filtering in Chroma DB fundamentally differs from traditional SQL-based filtering due to its emphasis on vector similarity and flexible metadata querying. 

* **SQL Databases:** Rely on structured schemas and declarative logic to retrieve exact matches using `WHERE` clauses, `LIKE`, or `CONTAINS` operators.
* **Chroma DB:** Designed for unstructured data and semantic search, supporting complex, context-aware queries tailored for AI-driven applications.

---

## 2. The Two Primary Filter Types

| Filter Type | Description | SQL Comparison |
| :--- | :--- | :--- |
| **Metadata Filtering** | Filters based on document metadata attributes (e.g., `{"topic": "history"}`, `{"date": "2023-01-15"}`). | Similar to SQL `WHERE` clauses, but more flexible and combinable with vector search. |
| **Document Filtering** | Filters based on document content using keyword presence (e.g., `$contains`, `$not_contains`). Also referred to as **full-text search**. | Comparable to SQL's `CONTAINS` or `LIKE` operators, but enhanced by vector integration. |

---

## 3. Metadata Filtering (`where` parameter)
Metadata filtering is performed using the `where` parameter inside `.query()`, `.get()`, or `.delete()` methods.

### Basic Syntax
To find exact matches for a metadata key:
```python
collection.get(
    where={"key": "value"}
)
