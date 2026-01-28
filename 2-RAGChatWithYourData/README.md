# RAG Chat With Your Data

## Overview

This module contains notebooks exploring **Retrieval Augmented Generation (RAG)** techniques for building AI systems that leverage external knowledge bases.

## Notebooks

### 1-DocumentLoading.ipynb
Loading documents from various sources:
- PDF files (PyPDFLoader)
- YouTube audio (YoutubeAudioLoader with OpenAI Whisper)
- Notion directories
- Generic blob loaders

### 2-DocumentSplitting.ipynb
**Text splitting strategies for RAG systems:**

#### Key Topics
1. **Character-based splitting** - CharacterTextSplitter vs RecursiveCharacterTextSplitter
2. **Semantic preservation** - Hierarchical separators (paragraphs → sentences → words)
3. **Token-aware splitting** - TokenTextSplitter for accurate LLM context management
4. **Format-specific splitting** - MarkdownHeaderTextSplitter with metadata preservation

#### Production Guidelines
- **Chunk size**: 500-2000 tokens (balance between context and granularity)
- **Chunk overlap**: 10-20% of chunk size for context continuity
- **Metadata preservation**: Always track source, page, section for traceability
- **Splitter selection**:
  - General text → `RecursiveCharacterTextSplitter`
  - Structured data → `CharacterTextSplitter`
  - LLM context windows → `TokenTextSplitter`
  - Markdown/hierarchical → `MarkdownHeaderTextSplitter`

## System Requirements

- Python 3.12 (required for pydub/audioop compatibility)
- ffmpeg (for YouTube audio processing)

See main [README.md](../README.md) for setup instructions.
