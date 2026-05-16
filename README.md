# 🧠 Agentic RAG Workflow with LangGraph & Gemini

This repository demonstrates an advanced, stateful Retrieval-Augmented Generation (RAG) architecture built using **LangGraph**, **LangChain**, and **Google Gemini**. 

Unlike standard linear RAG pipelines (Query ➡️ Retrieve ➡️ Generate), this agent implements a dynamic, self-reflective workflow. It retrieves information, rigorously evaluates the relevance of the retrieved context using an LLM-as-a-judge (enforced via Pydantic schemas), and autonomously decides whether to generate an answer or terminate the process to prevent hallucinations.

## 🏗️ Architecture & Workflow

The system is modeled as a State Graph with the following nodes and conditional routing:

1. **Retrieve Node:** Queries a local ChromaDB vector store for documents relevant to the user's input.
2. **Grade Documents Node (The Evaluator):** Uses `gemini-2.5-flash` to evaluate the retrieved documents. It utilizes structured outputs to enforce a strict binary (`yes`/`no`) grading schema. Irrelevant documents are filtered out.
3. **Conditional Routing:** * If relevant documents exist ➡️ Route to **Generate**.
   * If all documents are irrelevant ➡️ Route to **End** (preventing hallucinations).
4. **Generate Node:** Synthesizes the final response strictly grounded in the validated context.

## 🛠️ Tech Stack

* **Orchestration:** LangGraph, LangChain (`langchain-core`, `langchain-community`)
* **LLM & Embeddings:** Google Gemini (`gemini-2.5-flash`, `gemini-embedding-2`) via `langchain-google-genai`
* **Vector Database:** ChromaDB
* **Data Validation:** Pydantic (for strict JSON schema enforcement)
* **Environment:** Google Colab

## 🚀 Getting Started (Google Colab)

This project is optimized to run seamlessly in a Google Colab environment.

### 1. Prerequisites
* A Google Account to access Google Colab.
* A Gemini API Key (Generate one for free at [Google AI Studio](https://aistudio.google.com/app/apikey)).

### 2. Secure API Key Setup
Do not hardcode your API key into the notebook. Use Colab's secure Secrets manager:
1. Open your Colab notebook.
2. Click the **🔑 Secrets** icon on the left sidebar.
3. Click **Add new secret**.
4. Name the secret exactly: `GEMINI_API_KEY`.
5. Paste your Gemini API key into the Value field.
6. Toggle **Notebook access** to ON.

### 3. Installation
Run the following command in your first Colab cell to ensure all dependencies are resolved to their latest mutually compatible versions:
!pip install -q -U langchain langgraph langchain-google-genai langchain-community chromadb tiktoken sentence-transformers pydantic




## 📂 Code Execution Flow

The main script is structured into the following logical blocks:

1. **Environment Setup:** Securely loads the API key from Colab Secrets and initializes the Gemini models.
2. **Document Ingestion:** Automatically creates a `data/` directory, writes a sample knowledge base (`knowledge_base.txt`), chunks the text, and embeds it into ChromaDB.
3. **State Definition:** Defines the `AgentState` dictionary to track the question, retrieved documents, and generated output across nodes.
4. **Graph Construction:** Defines the Python functions for `retrieve`, `grade_documents` (using Pydantic), and `generate`, and wires them together using LangGraph's `StateGraph`.
5. **Execution:** Streams the graph execution step-by-step so you can observe the agent's internal decision-making process in real-time.

## 💡 Example Output

When running the test query: *"What library is used for stateful multi-actor systems?"*

```text
---NODE: RETRIEVE---
Finished step: retrieve
---NODE: GRADE DOCUMENTS---
---EDGE: DECIDE TO GENERATE---
---DECISION: DOCUMENTS RELEVANT. GENERATING ANSWER.---
Finished step: grade_documents
---NODE: GENERATE---
Finished step: generate

Final Answer:
LangGraph is a library used for building stateful, multi-actor applications with LLMs.

```

---

*Created as part of an advanced AI Engineering portfolio.*

```

```
