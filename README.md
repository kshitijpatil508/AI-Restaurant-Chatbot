# The Saffron Table — AI Restaurant Chatbot 🍽️

**An intelligent, RAG-powered chatbot for "The Saffron Table" restaurant, capable of answering customer queries using knowledge stored in Supabase.**

* **Live Website:** [The Saffron Table](https://the-saffron-table.netlify.app/)

---

## 📖 Overview

This n8n workflow powers a customer support chatbot for **The Saffron Table**. It utilizes **Google Gemini** models to understand natural language and retrieves specific restaurant information (menus, hours, policies) from a **Supabase Vector Store**.

The workflow consists of two distinct operational pipelines:
1.  **Knowledge Ingestion:** Fetches documents from Google Drive, creates embeddings, and stores them in Supabase.
2.  **Chat Interface:** A conversational agent that remembers context via Postgres and queries the vector store to provide accurate answers.

---

## Workflow Diagram
<img width="1483" height="876" alt="image" src="https://github.com/user-attachments/assets/7ccfdc7d-8c15-4bcf-9fc4-1201e882bb71" />

## Demo Preview
<img width="1902" height="932" alt="image" src="https://github.com/user-attachments/assets/db84f180-5751-44c3-a318-3f83efd2f7fc" />

---

## ✨ Core Features & Technology Stack

| Feature | Description | Technology Used (n8n Nodes) |
| :--- | :--- | :--- |
| **1. AI Chat Agent** | Acts as the chatbot for the restaurant, processing user queries. | `@n8n/n8n-nodes-langchain.agent` |
| **2. LLM Backend** | Powered by Google's **Gemini-2.5-flash-lite** model for fast, accurate responses. | `lmChatGoogleGemini` |
| **3. RAG (Retrieval)** | Looks up relevant restaurant data from Supabase to answer queries. | `vectorStoreSupabase` (Retrieve as tool) |
| **4. Conversation Memory** | Remembers previous messages in the session using a Postgres database. | `memoryPostgresChat` |
| **5. Knowledge Ingestion** | Scans a specific Google Drive folder, downloads files, and processes them. | `googleDrive`, `Loop Over Items`, `Default Data Loader` |
| **6. Vector Embeddings** | Converts document text into vectors using Gemini Embeddings. | `embeddingsGoogleGemini` |

---

## 🛠️ Setup and Installation Guide

### Files
* **Workflow File:** `RESTAURANT.json` (Import this into your n8n instance).

### 1) Import the workflow
1.  Open your n8n editor.
2.  Import `RESTAURANT.json`.

### 2) Configure Credentials
You must set up the following credentials in n8n for the workflow to function:

| Credential Name | Purpose |
| :--- | :--- |
| **Google Gemini(PaLM) Api account** | Used for the Chat Model and Embeddings. |
| **Supabase Restaurant Account** | Used to store and retrieve vector data. |
| **Google Drive account** | Used to access the knowledge base folder. |
| **Postgres account** | Used for chat memory persistence. |

### 3) Knowledge Base Setup (First Run)
Before the chatbot can answer specific questions, you must populate the vector database:
1.  Ensure your restaurant documents (PDFs, text files) are in the Google Drive folder with ID `1sfY0i-njfVLLvl63c7bNhgAsZ_TLxTxf` (or update the **Search files and folders** node with your own Folder ID).
2.  Click **Execute Workflow** manually (or trigger the "When clicking ‘Execute workflow’" node).
3.  The workflow will:
    * Search the folder.
    * Loop through files and download them.
    * Generate embeddings and insert them into the `documents` table in Supabase.

### 4) Chat Interface
The chat flow is triggered by the **When chat message received** node.
* The AI Agent is configured with the system message: *"You are a chatbot for 'The Saffron Table' restaurant, if anything related to restaurant comes make use of the tool."*.
* Embed this chat interface into your website or use the n8n testing chat window.

---

## 🎯 Workflow Logic

### Ingestion Pipeline (Manual Trigger)
`Manual Trigger` → `Search Drive` → `Loop` → `Download` → `Loader` → `Supabase Insert`
* *Purpose:* Keeps the AI's knowledge up to date with files from Google Drive.

### Chat Pipeline (Chat Trigger)
`Chat Trigger` → `If (Input Exists)` → `AI Agent` → `Respond to Chat`
* *Tools:* The Agent calls the **Supabase Vector Store** tool to look up context.
* *Memory:* The Agent uses **Postgres Chat Memory** (Window Length: 510) to maintain conversation history.

---

## 🤝 Contribution

If you'd like to improve the prompt engineering or add new data sources:
1.  Fork the repo.
2.  Update the System Message in the **AI Agent** node.
3.  Submit a PR.
