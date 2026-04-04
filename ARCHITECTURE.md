# Architecture Details

## System Design Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER INTERFACE                           │
│                      (React/Next.js)                            │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Upload PDF  │  Chat Messages  │  Input Question        │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │ HTTP (JSON)
                             ↓
┌─────────────────────────────────────────────────────────────────┐
│                      API SERVER                                 │
│                     (FastAPI)                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  /upload  /ask  /document  /clear  /health             │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
           ┌─────────────────┼─────────────────┐
           ↓                 ↓                 ↓
      ┌─────────────┐  ┌──────────────┐  ┌────────────┐
      │ PDF Parser  │  │ QA Engine    │  │ OpenAI    │
      │ (PyPDF2)    │  │ (RAG)        │  │ API       │
      └─────────────┘  └──────────────┘  └────────────┘
           │                 │
           ↓                 ↓
      ┌──────────────────────────────┐
      │   Vector Store (Chromadb)    │
      │  (In-memory Embeddings)      │
      └──────────────────────────────┘
```

## Component Architecture

### Backend (Python/FastAPI)

```
main.py
├── PDFUploadEndpoint
│   ├── File validation
│   ├── PDF extraction
│   └── Chunk creation
│
├── QuestionEndpoint
│   ├── Query embedding
│   ├── Similarity search
│   └── LLM generation
│
└── Utility Endpoints
    ├── Document info
    ├── Document clear
    └── Health check

pdf_processor.py
├── PDFProcessor class
│   ├── extract_text_with_pages()
│   ├── chunk_text()
│   ├── validate_pdf()
│   └── _split_text()

qa_engine.py
├── QAEngine class (Chromadb integration)
│   ├── load_document()
│   ├── retrieve_relevant_chunks()
│   ├── answer_question()
│   └── clear_document()
```

### Frontend (React/TypeScript)

```
Layout
├── app/page.tsx (Main page)
├── app/layout.tsx (Root layout)
└── app/globals.css (Global styles)

Components
├── Header (title + doc info)
├── PDFUpload (file input)
├── ChatWindow (messages)
├── ChatMessage (single message)
├── ChatInput (question input)

Library
├── api.ts (API client)
└── store.ts (Zustand state management)
```

## Data Processing Pipeline

### Upload Phase
```
PDF File
   ↓
[PyPDF2.PdfReader]
   ↓
Extract text with page tracking
   ↓
Split into overlapping chunks (1000 chars, 200 overlap)
   ↓
Generate embeddings (OpenAI text-embedding-3-small)
   ↓
Store in Chromadb
   ↓
✓ Ready for Q&A
```

### Query Phase
```
User Question
   ↓
[Embed question using same model]
   ↓
[Calculate cosine similarity with stored chunks]
   ↓
[Retrieve top 3 most similar chunks]
   ↓
[Format context: "Page X: [text]"]
   ↓
[Send to GPT-4-turbo with system prompt]
   ↓
[Extract answer and page references]
   ↓
[Return with source citations]
```

## API Contract

### Request/Response Examples

**POST /upload**
```
Request:
multipart/form-data with file: PDF_FILE

Response:
{
  "success": true,
  "message": "PDF 'document.pdf' uploaded successfully",
  "document": {
    "name": "document.pdf",
    "pages": 10,
    "chunks": 45
  }
}
```

**POST /ask**
```
Request:
{
  "question": "What is X?"
}

Response:
{
  "answer": "According to the document, X is...",
  "sources": [
    {
      "text": "X is defined as...",
      "page": 3,
      "relevance": 0.95
    }
  ],
  "document_info": {
    "name": "document.pdf",
    "pages": 10,
    "chunks": 45
  }
}
```

## Performance Characteristics

### Time Complexity
- PDF Upload: O(n) where n = number of pages
- Query: O(m) where m = number of chunks (using Chromadb optimizations)

### Space Complexity
- Vector embeddings: ~1536 dimensions per chunk (OpenAI embedding)
- Average PDF: 40-50 chunks = ~6-7 MB in memory

### Network
- Average response time: 2-3 seconds
- Bottleneck: OpenAI API latency (1-2 sec)

## State Management Flow

```
User Action (Upload/Ask)
        ↓
[React Component]
     ↓ (dispatch action)
[Zustand Store]
     ↓ (fetch data)
[API Handler]
     ↓ (send request)
[FastAPI Backend]
     ↓ (process)
[Response]
     ↓ (update store)
[Re-render Component]
```

## Error Handling Strategy

```
Layer 1: Backend Validation
- File type validation
- PDF integrity check
- Empty question validation

Layer 2: API Error Handling
- Try-catch blocks
- Detailed error messages
- HTTP status codes

Layer 3: Frontend Error Handling
- User-friendly error messages
- Error toast notifications
- Graceful degradation

Layer 4: User Communication
- Clear error messages
- Suggestions for resolution
- Retry buttons
```

## Scaling Considerations

### Current Limitations
- Single document at a time (memory inefficient)
- In-memory storage (not persistent)
- No rate limiting
- Synchronous processing

### For Production
1. **Persistence**: PostgreSQL + Pgvector
2. **Distributed**: Multiple API servers behind load balancer
3. **Caching**: Redis for embedding cache
4. **Queue**: Celery for async processing
5. **Monitoring**: Prometheus + Grafana

---

For detailed implementation, see the code comments in each file.
