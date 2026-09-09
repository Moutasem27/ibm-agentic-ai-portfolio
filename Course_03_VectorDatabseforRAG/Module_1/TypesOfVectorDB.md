# Types of Vector Databases & Architectural Paradigms: A Comprehensive Summary

---

## 1. Core Categories of Vector Databases
Vector databases are classified into several architectural types based on how they store, index, and manage data:

* **In-Memory Vector Databases:** Store vectors directly in memory to enable swift read-and-write operations, making them ideal for real-time analytics and recommendation systems. Examples include RedisAI and Torchserve.
* **Disk-Based Vector Databases:** Store vectors on disk using sophisticated indexing and compression techniques to handle large datasets that exceed memory capacity. Examples include Annoy, Milvus, and ScaNN.
* **Distributed Vector Databases:** Spread vector data across multiple nodes or servers to provide horizontal scalability and high throughput for massive datasets. Examples include FAISS, Elasticsearch with Vector Plugin, and Dask-ML.
* **Graph-Based Vector Databases:** Model data as graphs with nodes and edges representing vector attributes or embeddings, capturing complex relationships for graph analytics, knowledge graphs, and social network analysis. Examples include Neo4j, Amazon Neptune, and TigerGraph.
* **Time-Series Vector Databases:** Manage data collected over time intervals and represent it as vectors to analyze temporal patterns, IoT metrics, and system anomalies. Examples include InfluxDB, TimescaleDB, and Prometheus.

---

## 2. Dedicated Vector Databases vs. Databases Supporting Vector Search

### Dedicated Vector Databases
Dedicated vector databases are specialized systems optimized to store, index, query, and analyze vector data quickly and accurately.
* **Key Characteristics:** Utilize unique data structures like inverted indexes, product quantization, and locality-sensitive hashing (LSH). They support advanced vector operations such as nearest-neighbor search, similarity search, and distance calculations while offering high scalability and customizable indexing parameters.
* **Notable Vendors:** FAISS, Annoy, and Milvus.

### Databases That Support Vector Search
These are conventional database systems or data processing frameworks extended with add-ons or plugins to handle vector data alongside traditional queries.
* **Key Characteristics:** Store vector data as BLOBs, arrays, or user-defined types (UDTs), often indexing them using standard or custom index structures or external libraries. While versatile, they may not be as heavily optimized for raw speed as dedicated vector systems.
* **Notable Vendors:** SingleStore (integrated with IBM watsonx.ai), Elasticsearch (via vector add-on), PostgreSQL (with PostGIS/vector extensions), MySQL, RedisAI, Apache MongoDB, and Apache Cassandra.
