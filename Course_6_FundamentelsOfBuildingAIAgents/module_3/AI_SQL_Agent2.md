# Implementing LangChain's AI-Powered SQL Agent



## 1. Environment Setup & Prerequisites
To prepare your development environment and install the necessary dependencies:
- **Virtual Environment:** Create a Python virtual environment using the command `virtualenv my_env` to manage project dependencies[cite: 1].
- **Required Libraries:** Install essential packages including `ibm-watsonx-ai`, `LangChain`, and `mysql-connector-python` to facilitate AI integration and database connectivity[cite: 1].
- **SQL Server Launch:** Launch your MySQL server in the development environment (allow approximately 15 seconds for the database to become active) and open the MySQL Command Line Interface (CLI)[cite: 1].

---

## 2. Database Preparation (Chinook Database)
This implementation uses the **Chinook database**, a sample dataset representing a digital media store that includes various interconnected tables (such as albums, artists, tracks, and media)[cite: 1].
- **Fetching the Script:** Load the database script using `wget` along with the URL of the Chinook MySQL SQL file[cite: 1].
- **Loading Data:** In the MySQL terminal window, run `_SOURCE chinook-mysql.sql` to load the contents into your database[cite: 1].
- **Verification:** Run `_SHOW DATABASES;` to verify creation, then test reading data by switching to the database and running a count (e.g., `USE Chinook; SELECT COUNT(*) FROM Album;`)[cite: 1].

---

## 3. Loading the IBM watsonx.ai Granite Model
To integrate the IBM watsonx.ai Granite large language model into LangChain:
1. **Credentials & Parameters:** Create a dictionary to store credential information (including project ID `"skills-network"`, API keys, and optional space ID)[cite: 1]. Initialize generation parameters:
   - `MAX_NEW_TOKENS`: Defines the maximum number of tokens the model can generate in a single run[cite: 1].
   - `TEMPERATURE`: Adjusts output randomness (lower values make output more predictable/deterministic, while higher values introduce creativity and variability)[cite: 1].
   - `verify`: Set to `false` if working in a local or unsecured environment to disable SSL verification[cite: 1].
2. **Model Integration:** Import the model class from `ibm_watsonx_ai.foundation_models` and `watsonxLLM` from `langchain_ibm`[cite: 1]. Wrap the model using the `watsonxLLM` class to enable seamless integration with LangChain's features (such as chains, tools, chain-of-thought processing, reasoning steps, chatbots, SQL agents, and vector store semantic search)[cite: 1].

---

## 4. Connecting to MySQL and Creating the SQL Agent
To connect the AI model to the database:
1. **Connection Parameters:** Define your MySQL username, password, host/IP address, port (default `3306`), and database name (`Chinook`) to build a unified connection URI string[cite: 1].
2. **Database Connector:** Use LangChain's `SQLDatabase.from_URI` method to seamlessly integrate with the MySQL server[cite: 1].
3. **Agent Creation:** Set up the SQL agent using LangChain's `create_sql_agent` function by passing the LLM and the database (`db`), setting `verbose=True` to inspect complete thought processes and generated SQL queries, and configuring the agent type as a zero-shot agent that performs a reasoning step before acting[cite: 1].

---

## 5. Testing and Execution
- **Natural Language Query Test:** Test your setup with a query such as: *"How many Albums are listed in the database?"*[cite: 1]
- **Execution Flow:** The system sends the natural language query to the agent, the LLM translates it into SQL, the query runs against the database, and the agent responds with the correct count (confirming 347 albums, matching a direct SQL query)[cite: 1].
