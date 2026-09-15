# Using Natural Language to Create Data Visualizations with LangChain Pandas Agents


## 1. Overview of the LangChain Pandas Agent
- **Purpose:** Ideal for exploration and rapid prototyping using dynamic code execution (*Note: Not recommended for production environments unless comprehensive safeguards are in place*). Available within the `langchain-experimental` package.
- **Key Differences:** 
  - Uses a pre-configured set of functions and prompts to save time and effort.
  - Operates directly on an existing Pandas DataFrame provided by the user.
  - Accepts natural language prompt inputs and responds with appropriate answers (values, summaries, or visualizations).

---

## 2. Setup and Initialization

### Step 1: Prepare the Dataset
- Import Pandas (`import pandas as pd`).
- Load your DataFrame object (`df`). For example, you can use the Student Alcohol Consumption CSV formatted dataset by UCI Machine Learning.
- Display dataset headers and initial rows using `df.head()` to inspect parameters (e.g., `sex` represented as `M`/`F` and `age` as numeric values between 15 and 22).

### Step 2: Configure Credentials and the watsonx.ai Model
- Import generation parameters from `ibm_watsonx_ai.metanames` (`GenTextParamsMetaNames` as `GenParams`).
- Create a credential dictionary specifying the model ID (e.g., Llama 3 70B), project ID, space ID, and generation parameters (such as token limits).
- Load the watsonx LLM and connect it to LangChain using LangChain's watsonx extension.

### Step 3: Initialize the Pandas DataFrame Agent
- Import `create_pandas_dataframe_agent` from LangChain.
- Pass the LLM and the DataFrame (`df`) into the agent constructor.
- Set `verbose=True` to inspect detailed execution steps, and `return_intermediate_steps=True` to view the generated Python code for debugging or verification.

---

## 3. Natural Language Analysis and Visualization

### Asking Data Questions
You can query the dataset using plain English:
- **Query Example:** *"How many rows are in this file?"*
  - **Agent Response:** 395 rows. (Inspecting `intermediate_steps` reveals the underlying generated code: `len(df)`).
- **Query Example:** *"How many students are 18 years old?"*
  - **Agent Response:** 82 students. (Underlying code filters the DataFrame for rows where age equals 18 and counts matches).

### Generating Visualizations
- Verbalize or type requests for visual outputs, such as: *"Plot the gender count with bars."*
- The agent interprets terms like "gender" to target the correct column (`sex`) and generates clear charts within seconds without requiring manual coding.

---

## 4. Best Practices for Safe Data Analysis
To ensure safe and effective AI-driven data analysis, follow these guidelines:
- **Use Sandboxed Environments:** Prevent unintended modifications to live data and mitigate prompt injection risks that could execute malicious code.
- **Design Clear Prompts:** Write specific instructions to avoid ambiguous responses.
- **Validate with Human Expertise:** Combine LLM analysis with human oversight to verify that the agent analyzed the correct data and returned accurate results.
- **Iteratively Refine:** Continuously improve your prompts and analysis workflows for better performance.
