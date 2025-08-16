# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Running the Application
- **Start the server**: `./run.sh` (makes directories and starts uvicorn)
- **Manual start**: `cd backend && uv run uvicorn app:app --reload --port 8000`
- **Install dependencies**: `uv sync`

### Environment Setup
- Create `.env` file in root with `ANTHROPIC_API_KEY=your_key_here`
- Requires Python 3.13+ and uv package manager

### Application URLs
- Web Interface: `http://localhost:8000`
- API Documentation: `http://localhost:8000/docs`

## Architecture Overview

### Core System Design
This is a **Retrieval-Augmented Generation (RAG) system** that processes course materials and answers questions using semantic search + AI generation.

**Key Components:**
- **RAGSystem** (`backend/rag_system.py`): Main orchestrator that coordinates all components
- **VectorStore** (`backend/vector_store.py`): ChromaDB-based semantic search using sentence-transformers
- **AIGenerator** (`backend/ai_generator.py`): Anthropic Claude integration with tool support
- **DocumentProcessor** (`backend/document_processor.py`): Processes course documents into chunked content
- **ToolManager** (`backend/search_tools.py`): Manages AI tool functions for semantic search

### Data Flow
1. Course documents (PDF/DOCX/TXT) are processed into semantic chunks
2. Chunks stored in ChromaDB with embeddings (all-MiniLM-L6-v2 model)
3. User queries trigger tool-based search via Claude AI
4. AI uses CourseSearchTool to find relevant content and generates responses
5. Session management maintains conversation context

### FastAPI Application Structure
- **Main app** (`backend/app.py`): FastAPI server with CORS, static file serving for frontend
- **API endpoints**: `/api/query` for questions, `/api/courses` for analytics
- **Frontend**: Vanilla HTML/JS/CSS in `/frontend/` directory
- **Auto-loading**: Documents from `/docs/` folder loaded on startup

### Key Configuration
- **Config** (`backend/config.py`): Centralized settings using environment variables
- **Chunk settings**: 800 char chunks with 100 char overlap
- **Model**: claude-sonnet-4-20250514 for AI generation
- **Storage**: ChromaDB persisted to `./chroma_db`

### Session Management
- **SessionManager** (`backend/session_manager.py`): Maintains conversation history
- **MAX_HISTORY**: 2 previous exchanges kept per session
- Sessions auto-created if not provided in requests

## Important Notes
- Documents are only added if not already existing (checks by title)
- System uses Claude AI tools for semantic search instead of direct vector queries
- Frontend served as static files with no-cache headers for development
- CORS enabled with wildcard origins for development
- using uv to run the project
- using uv to manage python package dependency