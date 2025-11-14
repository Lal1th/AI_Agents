# AI Agent & Multi-Agent Examples for Data Analysis

This project, contained in the `AI_Agent.ipynb` notebook, demonstrates the creation of sophisticated AI agents using LangChain and Google's Gemini models. It showcases several patterns for building agents that can query data, perform analysis, generate visualizations, and collaborate in a multi-agent workflow.

The primary example dataset used is an emissions audit log (`su_emission_audit.parquet`).

##  Features & Demonstrations

The notebook is divided into three main demonstrations:

### 1. Multi-Tool ReAct Agent

This agent uses the **ReAct (Reasoning and Acting)** framework to intelligently select from a diverse set of tools to answer complex, multi-step questions.

* **Tools Provided:**
    * **`Knowledge_Search`**: A RAG (Retrieval-Augmented Generation) pipeline built with FAISS and HuggingFace embeddings to answer questions about the data's schema and context.
    * **`DuckDB_Query_Executor`**: An in-memory SQL tool (using DuckDB) to query the `su_emission_audit.parquet` file directly.
    * **`Weather_For_Coordinates`**: A custom tool that fetches historical weather data from the Open-Meteo API for a given latitude, longitude, and date.
    * **`Python_REPL`**: A tool that can execute arbitrary Python code, primarily used for data analysis (pandas) and plotting (matplotlib).

* **Example Tasks:**
    * **Multi-Step Query:** "Find the location and date of the first emission event, then get the historical weather for that location and date."
    * **Data Analysis & Plotting:** "Perform data analysis and show some plots." The agent proceeds to query the data using SQL and then uses the Python REPL to generate and display three different bar charts.

### 2. Tool-Calling Agent (Python-Only)

This agent uses the modern `create_tool_calling_agent` constructor, which is optimized for models like Gemini. It demonstrates how a *single, powerful tool* (the Python REPL) can be used to perform complex, end-to-end analysis.

* **Tools Provided:**
    * **`Python_REPL`**: The agent's only tool. It writes and executes Python code to load, analyze, and summarize data.

* **Example Tasks:**
    * **"Executive Summary"**: The agent is asked to generate a detailed executive summary of the dataset. It uses pandas to get `df.info()`, `df.describe()`, and `df.columns()` to generate a comprehensive report on key findings, data quality, and recommendations.
    * **"Explain this Emission"**: The agent is asked to pick a random emission event, explain it, and find its historical context. It uses pandas to `sample()` an observation and then query the main dataframe for all other events from the same asset.

### 3. Multi-Agent System (LangGraph)

This is the most advanced example, demonstrating an "Analyst-Critic" workflow built using **LangGraph**. This pattern involves two agents collaborating to improve the quality of the final answer.

* **Analyst Agent:** A tool-calling agent (like in Demo #2) responsible for performing the data analysis and visualization.
* **Critic Agent:** An LLM-based agent that reviews the Analyst's work.
    * If the work is satisfactory, it outputs "APPROVE" and the workflow ends.
    * If the work is flawed (e.g., code errors, incomplete analysis), it provides specific, actionable feedback.
* **Workflow:** The graph is cyclical. The Analyst's output is passed to the Critic. The Critic's feedback is passed back to the Analyst to try again. This loop continues until the Critic approves or a recursion limit is hit.

## 🛠️ Core Technologies

* **LLM Framework:** LangChain & LangGraph
* **LLM:** Google Gemini (via `langchain_google_genai`)
* **Data Analysis:** Pandas, DuckDB
* **Visualization:** Matplotlib
* **RAG:** FAISS (vector store) & Sentence Transformers (embeddings)
* **Agent Tools:** Python REPL, Requests

## ⚙️ Setup and Installation

1.  **Clone the Repository:**
    ```bash
    git clone 
    cd 
    ```

2.  **Install Dependencies:**
    A `requirements.txt` is not provided, but you can install the necessary packages from the notebook's `pip` command:
    ```bash
    pip install pandas requests sentence-transformers langchain langchain-community langchain_google_genai faiss-cpu google-generativeai duckdb matplotlib langchain_experimental langchain-huggingface langgraph
    ```

3.  **Set API Key:**
    This project requires a Google Gemini API key.
    * Create a key at [Google AI Studio](https://aistudio.google.com/app/apikey).
    * Set it as an environment variable named `GEMINI_API_KEY` or `GOOGLE_API_KEY`.
    * If using Google Colab, you can set this in the "Secrets" tab.

4.  **Add Data:**
    The notebook requires the `su_emission_audit.parquet` file. This file is **not** included in the repository. You must place this file in the same directory as the notebook for the agents to function.

## Usage

Once the setup is complete, you can run the `AI_Agent.ipynb` notebook cell by cell in a Jupyter environment (like VS Code, Jupyter Lab, or Google Colab) to see each agent in action.

**Note:** The multi-agent LangGraph example (the final cell) is designed to show the *process* of an analyst-critic loop. In the provided notebook log, it hits a recursion limit, as the analyst agent repeatedly makes a `KeyError` and the critic correctly sends it back for revision. This demonstrates the robustness of the "critic" pattern.
