# RAG Agent — Multi-format Document Chatbot

An end-to-end RAG (Retrieval Augmented Generation) system built with **n8n**, combining automated document ingestion with a conversational AI agent that answers questions based on your own knowledge base.

Built by [Juan Pedro Torres](https://www.linkedin.com/in/juanpedrotorressefiraai) · [Sefira AI](https://sefiraai.com) — AI automation for SMEs and IT teams.

## What it does

This workflow has two connected pipelines:

### 1. Document Ingestion Pipeline
Automatically watches a Google Drive folder for new or updated files and processes them into a searchable knowledge base:

- **Trigger:** Google Drive (file created / file updated)
- **Format detection:** routes files by type using a Switch node
- **Extraction:** supports PDF, DOC, CSV and XLSX via dedicated extraction nodes
- **Embeddings:** generated with OpenAI Embeddings
- **Storage:** indexed into a **Supabase Vector Store**

### 2. Conversational RAG Agent
A chat-triggered AI agent that retrieves relevant context from the vector store and responds with grounded, accurate answers:

- **Trigger:** chat message received
- **Model:** OpenAI Chat Model
- **Memory:** persistent conversation history via Postgres Chat Memory
- **Retrieval tool:** queries the Supabase Vector Store for relevant document chunks
- **Output:** contextual answer based on retrieved knowledge, not hallucinated content

## Architecture

```
Google Drive (file created/updated)
        │
        ▼
   Edit Fields → Delete Row → Download File
        │
        ▼
   Switch (route by file type)
   ├── PDF  → Extract From PDF
   ├── DOC  → Extract From Text File
   ├── CSV  → Extract From CSV
   └── XLSX → Extract From Excel
        │
        ▼
   Aggregate → Summarize
        │
        ▼
   Embeddings (OpenAI) → Supabase Vector Store
```

```
Chat Message Received
        │
        ▼
   RAG Agent (OpenAI Chat Model + Postgres Memory)
        │
        ▼
   Tool: Supabase Vector Store (semantic search)
        │
        ▼
   Grounded response to user
```

## Stack

- **n8n** — workflow orchestration
- **OpenAI API** — chat completion + embeddings
- **Supabase** — vector store (pgvector) + Postgres for chat memory
- **Google Drive** — document source

## Why this matters

Most "RAG demos" stop at a single PDF and a static script. This workflow is built for **real operational use**: it ingests multiple file formats automatically, keeps the knowledge base in sync as documents change, and maintains conversational memory across sessions — the same architecture I implement for clients who need internal documentation assistants or customer support knowledge bases.

## Setup

1. Import `workflow.json` into your n8n instance
2. Connect your own credentials:
   - OpenAI API key
   - Supabase project (URL + service key)
   - Google Drive OAuth2
   - Postgres connection (for chat memory)
3. Set your target Google Drive folder as the trigger source
4. Activate the workflow

> Credentials are **not** included in the exported JSON — you'll need to reconnect your own.

## About

I'm Juan Pedro, founder of **Sefira AI**, an automation agency helping SMEs and IT teams implement practical AI solutions — from internal support bots to document-based assistants like this one.

📧 jtorres@sefiraai.com
🔗 [sefiraai.com](https://sefiraai.com)
