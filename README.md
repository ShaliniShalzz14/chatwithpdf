# Chat with PDF - Gemini-Powered Document Q&A

## Project Setup Instructions

### Backend
1. `cd backend`
2. `python -m venv venv; venv\\Scripts\\activate` (Windows) or `source venv/bin/activate`
3. `pip install -r requirements.txt`
4. Add `GEMINI_API_KEY=your_key` to `.env`
5. `python main_fixed_fixed.py` (runs on http://localhost:8000)

### Frontend
1. `cd frontend`
2. `npm install`
3. `npm run dev` (runs on http://localhost:3000)

## Architecture / Flow
```
PDF Upload → PDFProcessor (PyPDF2 chunking) → SentenceTransformer embeddings (all-MiniLM-L6-v2) → In-memory numpy store
Query → Embed → Cosine similarity → Top-3 chunks → Gemini-2.5-flash (RAG prompt) → Answer + sources
```

## AI Tools and Models Used
- **LLM**: Gemini-2.5-flash (Google Generative AI, primary); fallback gemini-1.5-flash
- **Embeddings**: Sentence Transformers all-MiniLM-L6-v2 (local, no API)
- **Libraries**: google-generativeai, sentence-transformers, numpy (cosine sim)

## Prompts Used
**System (implicit in code)**: Factual, document-only answers, cite sources, "Not available in document" if missing.

**Query Prompt**:
```
Answer STRICTLY from context.
CONTEXT: [chunks]
Q: {query}
Rules: Cite pages, no inference.
```

## Limitations
- Gemini API costs/quota
- English-focused (transformers bias)
- Single PDF per session
- In-memory store (lost on restart)
- PDF layout issues (tables/images ignored)

## Possible Improvements
- Multi-PDF support
- Persistent storage (SQLite/pgvector)
- Hybrid search (BM25 + semantic)
- Streaming responses
- Local LLM fallback (Ollama)
- UI: Dark mode, chat export, query suggestions
