🇬🇧 English | [🇸🇮 Slovenščina](README.sl.md)

# 🚀 Codex RAG Chatbot

An AI chatbot (RAG – Retrieval Augmented Generation) for **Codex d.o.o.**, allowing users to quickly retrieve information about products, technical specifications, distributors, and contact details based on the company's official sales catalog.

---

## 📋 Project Overview

Codex uses an extensive 143-page multilingual product catalog (Slovenian, English, German, Croatian, and Serbian). Searching for information in such a document is often time-consuming for users, so a RAG chatbot was developed that:

* understands questions in natural language,
* finds relevant information in the catalog,
* responds in the user's language,
* reduces the need for manual searching through documentation.

---

## 🎯 Goals

* Enable fast retrieval of information from the catalog.
* Reduce the time needed to find technical specifications and contact details.
* Provide answers based on the company's official data.
* Prevent model hallucinations through the use of vector search.

---

## 🏗️ System Architecture

The project consists of two separate n8n workflows.

### Workflow A – Catalog Indexing

```text
Manual Trigger
      │
      ▼
Google Drive (Download Markdown File)
      │
      ▼
Default Data Loader
      │
      ▼
Text Splitter
      │
      ▼
OpenAI Embeddings
      │
      ▼
Pinecone Vector Store (Insert)
```

This workflow runs whenever the catalog is updated and prepares the data for semantic search.

### Workflow B – Chatbot

```text
Chat Trigger
      │
      ▼
AI Agent
 ├─ GPT-4o mini
 ├─ Window Buffer Memory
 └─ Pinecone Vector Store Tool
          │
          ▼
   Relevant Context
```

The chatbot uses vector search to retrieve relevant information from the Pinecone database and then generates a response using the OpenAI model.

---

## 📥 Data Preparation

The original PDF catalog was converted to Markdown format using CloudConvert.

Data preparation process:

1. The PDF catalog is converted to Markdown.
2. The Markdown file is stored on Google Drive.
3. The n8n workflow downloads the file.
4. The text is split into smaller segments (chunks).
5. Embeddings are generated for each segment.
6. Embeddings are stored in Pinecone.

This approach enables efficient semantic search across the entire content of the catalog.

---

## 🧠 Pinecone Indexing

### Text Splitter

* Recursive Character Text Splitter
* Chunk size: ~1000 characters
* Overlap: ~200 characters

### Embeddings

Model:

```text
text-embedding-3-small
```

### Pinecone Index

```text
codexchatbot
```

Each chunk contains:

* text
* source
* blobType
* loc.lines.from
* loc.lines.to

---

## 🤖 AI Agent

### Chat Model

```text
GPT-4o mini
```

The model was chosen for its good balance between:

* response quality,
* response speed,
* operating cost.

### Memory

```text
Window Buffer Memory
```

Maintains context throughout the conversation.

### Retrieval Tool

```text
codex_catalog_search
```

The Pinecone Vector Store Tool retrieves relevant information from the catalog.

---

## 📜 Key Agent Rules

* For all content-related questions, the agent must use `codex_catalog_search`.
* It responds in the user's language.
* If information cannot be found, it directs the user to `info@codex.si`.
* It declines questions not related to the catalog or Codex.

---

## 💬 Chat Interface

**Title:**

```text
Codex Assistant
```

**Starter message:**

A short greeting and an overview of the topics the chatbot can help with.

---

## 🛠️ Tech Stack

| Component                  | Tool                           |
| --------------------------- | -------------------------------- |
| Automation / orchestration  | n8n                               |
| PDF → Markdown conversion    | CloudConvert                     |
| File storage                 | Google Drive                     |
| Embeddings                   | OpenAI text-embedding-3-small    |
| Vector database              | Pinecone                          |
| Chat model                   | GPT-4o mini                      |
| Memory                       | Window Buffer Memory              |

---

## 📁 Repository Structure

```text
codex-rag-chatbot/
├── README.md/
├── README.sl.md/
├── workflows/
│   ├── rag-upload-pinecone.json
│   ├── chatbot.json
├── data/
│   ├── CODEX_2010_katalog_NET_links.md
├── prompts/
│   ├── system_prompt_en.md
│   └── system_prompt_sl.md
│   └── tool_description.md
```

---

## 💡 Architectural Decisions

### Markdown as an Intermediate Format

Converting the PDF document to Markdown simplifies further processing and improves the quality of text segmentation.

### Chunking with Overlap

Overlap between segments reduces context loss at the boundaries of individual chunks.

### Separate Workflows

Indexing and the chatbot are separate processes, which makes maintenance easier and improves scalability.

### Mandatory Use of the Retrieval Tool

For all content-related answers, the agent must use Pinecone search, reducing the likelihood of hallucinations and increasing the reliability of responses.

---

## 🔐 Security

All API keys and credentials are stored exclusively in n8n Credentials.

The repository does not contain:

* OpenAI API keys,
* Pinecone API keys,
* Google Drive credentials,
* other sensitive data.

---

## 📌 Project Status

The project was developed as a practical implementation of a RAG system for use with the real sales catalog of Codex d.o.o., and represents an example of integrating n8n, OpenAI, and Pinecone into a production-ready AI assistant.
