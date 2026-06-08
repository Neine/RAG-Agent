# RAG Agent — n8n + Supabase + OpenAI

> A fully visual, no-code Retrieval-Augmented Generation (RAG) agent built with n8n.  
> Answers questions from your own documents — grounded, auditable, no hallucinations from the open web.

---

## What this does

This workflow lets you chat with your documents. Upload a file to Google Drive, and the agent will read it, embed it into a vector store, and answer natural language questions from its contents — while remembering your conversation across turns.

Built for enterprise use cases where **accuracy and auditability matter** (banking, compliance, operations).

---

## Architecture

```
INGESTION PIPELINE
Google Drive → Data Loader → OpenAI Embeddings → Supabase Vector Store

CHAT PIPELINE
User Message → AI Agent → Vector Retrieval (Supabase) → Grounded Response
                  ↕               
            OpenAI Chat Model + Postgres Memory
```

---

## Stack

| Component | Role |
|---|---|
| **n8n** | Workflow orchestration (no-code) |
| **Supabase** | Vector store for document embeddings |
| **OpenAI** | Chat model (`gpt-4o`) + text embeddings |
| **Postgres** | Persistent conversation memory |
| **Google Drive** | Document source |

---

## How to use

### 1. Import the workflow

1. Download [`rag-agent-workflow.json`](./rag-agent-workflow.json)
2. Open n8n → **Workflows** → **Import from file**
3. Select the downloaded JSON

### 2. Set up credentials

You'll need API keys / connections for:
- **OpenAI** — [platform.openai.com/api-keys](https://platform.openai.com/api-keys)
- **Supabase** — Project Settings → API → `service_role` JWT
- **Postgres** — your Supabase database connection string
- **Google Drive** — OAuth2 via n8n credentials

### 3. Set up Supabase vector table

Run this in your Supabase SQL editor:

```sql
create extension if not exists vector;

create table documents (
  id bigserial primary key,
  content text,
  metadata jsonb,
  embedding vector(1536)
);

create index on documents
using ivfflat (embedding vector_cosine_ops)
with (lists = 100);
```

### 4. Ingest your documents

- Trigger the **ingestion pipeline** manually (or connect a Google Drive file change trigger)
- Your documents will be chunked, embedded, and stored in Supabase

### 5. Start chatting

- Open the **Chat** panel in n8n
- Ask questions about your documents
- The agent retrieves only relevant chunks — no web search, no hallucinations

---

## Example

**User:** What are the operational hours?  
**Agent:** Based on the uploaded policy document, operational hours are Monday to Friday, 9am–6pm IST, with weekend support available on request.

---

## Why RAG matters in banking & enterprise

Traditional AI chatbots answer from the open internet — unpredictable and unauditable.  

RAG agents only know what you give them:
- ✅ Answers are traceable to source documents
- ✅ No data leaves your controlled environment (if self-hosted)
- ✅ Easy to update — swap documents, not models
- ✅ Works with internal policies, SOPs, compliance docs

---

## Author

**Neine Arora** — Vice President, Deutsche Bank  
Building at the intersection of banking operations and agentic AI.

[LinkedIn](https://linkedin.com/in/) · [GitHub](https://github.com/)

---

## License

MIT — feel free to fork, adapt, and build on this.
