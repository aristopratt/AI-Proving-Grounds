# RAG Chat With Your Data

## Overview

A complete implementation of a **Retrieval Augmented Generation (RAG)** system with conversational memory. This project demonstrates how to build production-ready AI chatbots that can answer questions using your own documents, with full source attribution and chat history.

The notebooks progress through the entire RAG pipeline from document loading to a production-ready chatbot interface.

---

## Project Structure

```
2-RAGChatWithYourData/
├── 1-DocumentLoading.ipynb          # Load documents from various sources
├── 2-DocumentSplitting.ipynb        # Split documents into optimal chunks
├── 3-VectorsAndEmbeddings.ipynb     # Create semantic embeddings
├── 4-RetrievalTechniques.ipynb      # Advanced retrieval strategies
├── 5-Q&A.ipynb                      # Complete RAG Q&A pipeline
├── 6-Memory&POC.ipynb               # Production chatbot with UI
├── 99-DPDPA.pdf                     # Sample document (Data Protection Act)
└── README.md                        # This file
```

---

## Notebooks

### 1️⃣ Document Loading
**File:** `1-DocumentLoading.ipynb`

Load documents from multiple sources:
- **PDF files** - PyPDFLoader for structured documents
- **YouTube audio** - YoutubeAudioLoader + OpenAI Whisper for transcription
- **Notion directories** - Direct Notion database integration
- **Generic loaders** - Web pages, text files, and more

**Key Concepts:** Document loaders, metadata extraction, multi-source ingestion

---

### 2️⃣ Document Splitting
**File:** `2-DocumentSplitting.ipynb`

Optimize text chunking for RAG systems.

**Key Topics:**
- **Character-based splitting** - `CharacterTextSplitter` vs `RecursiveCharacterTextSplitter`
- **Semantic preservation** - Hierarchical separators (paragraphs → sentences → words)
- **Token-aware splitting** - `TokenTextSplitter` for accurate LLM context management
- **Format-specific splitting** - `MarkdownHeaderTextSplitter` with metadata preservation

**Production Guidelines:**
- **Chunk size**: 500-2000 tokens (balance between context and granularity)
- **Chunk overlap**: 10-20% of chunk size for context continuity
- **Metadata preservation**: Always track source, page, section for traceability

**Splitter Selection:**
| Document Type | Recommended Splitter |
|--------------|---------------------|
| General text | `RecursiveCharacterTextSplitter` |
| Structured data | `CharacterTextSplitter` |
| LLM context windows | `TokenTextSplitter` |
| Markdown/hierarchical | `MarkdownHeaderTextSplitter` |

---

### 3️⃣ Vectors and Embeddings
**File:** `3-VectorsAndEmbeddings.ipynb`

Understand the foundation of semantic search.

**Key Topics:**
- **Embeddings fundamentals** - Converting text to numerical vectors (1536 dimensions)
- **Semantic similarity** - Cosine similarity for meaning-based comparison
- **Vector databases** - Efficient storage and retrieval with ChromaDB
- **Similarity search** - Finding relevant documents by meaning, not keywords

**Why This Matters:**
- Traditional keyword search fails with synonyms ("dogs" vs "canines")
- Embeddings capture semantic meaning in mathematical space
- Similar meanings → similar vectors → high similarity scores

**Real-World Applications:**
- RAG systems - Find relevant context for LLMs
- Semantic search - "Find documents about X" (not just containing "X")
- Recommendations - "More like this" based on content
- Clustering - Group similar documents automatically

---

### 4️⃣ Advanced Retrieval Techniques
**File:** `4-RetrievalTechniques.ipynb`

Go beyond basic similarity search with advanced retrieval strategies.

**Techniques Covered:**

| Technique | Problem It Solves | Use Case |
|-----------|------------------|----------|
| **Similarity Search** | Basic retrieval | General Q&A |
| **MMR** (Max Marginal Relevance) | Result redundancy | Need diverse perspectives |
| **Metadata Filtering** | Too broad results | Search within specific sections |
| **Self-Query Retrieval** | Complex user queries | Production chatbots |

**Self-Query Retrieval (⭐ Advanced):**
- LLM automatically extracts search terms and metadata filters from natural language
- Example: "What does page 5 say about consent?" → 
  - Search query: "consent"
  - Metadata filter: `{"page": 5}`

---

### 5️⃣ Question Answering (RAG Pipeline)
**File:** `5-Q&A.ipynb`

Complete RAG implementation for question answering.

**Pipeline:**
```
User Question → Embed Query → Similarity Search → Retrieve Documents → 
LLM + Context → Generated Answer + Citations
```

**Key Topics:**
- **Vector store connection** - Load persisted embeddings
- **Basic retrieval QA** - Simple Q&A with default settings
- **Custom prompts** - Control response format and prevent hallucinations
- **Source attribution** - Trace answers back to original PDF pages
- **Chain type comparison** - Evaluate `stuff`, `map_reduce`, and `refine` strategies

**Why RAG-based QA:**
1. **Grounded responses** - Reduce hallucinations with specific documents
2. **Source attribution** - Enable citations and audit trails
3. **Domain specialization** - Transform generic LLMs into domain experts
4. **Up-to-date information** - Refresh knowledge without retraining

---

### 6️⃣ Conversational RAG Chatbot (Production POC)
**File:** `6-Memory&POC.ipynb`

**Complete production-ready chatbot with:**
- ✅ Interactive Panel UI (web-based chat interface)
- ✅ Conversational memory (remembers chat history)
- ✅ RAG pipeline integration
- ✅ Source attribution (shows PDF pages used)
- ✅ Database loading and management
- ✅ Chat history management

**Features:**
- **Conversational Memory** - ConversationBufferMemory for multi-turn dialogue
- **Panel UI** - Interactive widgets for file upload and chat
- **Source Attribution** - Displays which PDF pages were used for each answer
- **Session Management** - Clear history while keeping database loaded

**Usage:**
1. Click "Load Database" to load the DPDPA PDF
2. Type questions and press Enter
3. View answers with source citations
4. Bot remembers previous context in the conversation

**Example Questions:**
- What are the penalties for a data breach?
- Who is a Data Fiduciary?
- What are their main obligations? _(uses memory from previous question)_

---

## System Requirements

**Prerequisites:**
- Python 3.12 (required for pydub/audioop compatibility)
- ffmpeg (for YouTube audio processing)
- OpenAI API key

**Installation:**
```bash
# Install ffmpeg (macOS)
brew install ffmpeg

# Install Python dependencies (handled by uv)
uv sync
```

**Environment Variables:**
Create a `.env` file in the project root:
```
OPENAI_API_KEY=your_api_key_here
```

---

## Quick Start

1. **Install dependencies:**
   ```bash
   uv sync
   ```

2. **Set up environment:**
   ```bash
   echo "OPENAI_API_KEY=your_key" > .env
   ```

3. **Run the chatbot:**
   Open `6-Memory&POC.ipynb` in Jupyter and execute all cells to launch the interactive UI.

4. **Or explore step-by-step:**
   Start with `1-DocumentLoading.ipynb` and progress through each notebook to understand the full pipeline.

---

## Sample Document

**99-DPDPA.pdf** - Digital Personal Data Protection Act (India)
- Used throughout the notebooks for demonstrations
- Contains legal text suitable for testing RAG systems
- Demonstrates handling of structured legal documents

---

## Key Technologies

- **LangChain** - RAG framework and chains
- **OpenAI** - LLM (GPT-3.5-turbo) and embeddings (text-embedding-ada-002)
- **ChromaDB** - Vector database for embeddings
- **Panel** - Interactive UI framework
- **PyPDF** - PDF document loading

---

## Architecture Overview

```
┌─────────────────┐
│  Document Load  │ → PDF, YouTube, Notion
└────────┬────────┘
         ↓
┌─────────────────┐
│ Text Splitting  │ → Chunks with metadata
└────────┬────────┘
         ↓
┌─────────────────┐
│   Embeddings    │ → Vector representation
└────────┬────────┘
         ↓
┌─────────────────┐
│  Vector Store   │ → ChromaDB persistence
└────────┬────────┘
         ↓
┌─────────────────┐
│   Retrieval     │ → Similarity search
└────────┬────────┘
         ↓
┌─────────────────┐
│  LLM + Memory   │ → Conversational Q&A
└────────┬────────┘
         ↓
┌─────────────────┐
│   Panel UI      │ → Production interface
└─────────────────┘
```

---

## Production Considerations

**Chunk Size Selection:**
- **Small chunks (500 tokens)** - More precise, but less context
- **Large chunks (2000 tokens)** - More context, but may include irrelevant info
- **Recommended:** 1000 tokens with 150 overlap

**Retrieval Settings:**
- **k=4** - Good balance for most use cases
- Increase k for comprehensive answers
- Decrease k for focused, concise responses

**Chain Types:**
- **stuff** - Fast, best for small documents (used in chatbot)
- **map_reduce** - Parallel processing for large documents
- **refine** - Iterative improvement for complex questions

---

## Next Steps

- Experiment with different embedding models
- Implement hybrid search (semantic + keyword)
- Add user authentication and session persistence
- Deploy as a web service (FastAPI + Docker)
- Integrate with production vector databases (Pinecone, Weaviate)

---

For more information, see the main project [README.md](../README.md).
