# API Reference

## Base URL
```
http://localhost:8000
```

## Authentication
Currently: None (add for production)

## Endpoints

### POST /upload
Upload a PDF document for processing

**Request:**
```
Content-Type: multipart/form-data

Body:
- file: [PDF File]
```

**Response (Success - 200):**
```json
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

**Response (Error - 400):**
```json
{
  "detail": "File must be a PDF"
}
```

**Example:**
```bash
curl -X POST -F "file=@document.pdf" http://localhost:8000/upload
```

---

### POST /ask
Ask a question about the uploaded PDF

**Request:**
```json
{
  "question": "What is the main topic?"
}
```

**Response (Success - 200):**
```json
{
  "answer": "According to the document...",
  "sources": [
    {
      "text": "The main topic is... [truncated]",
      "page": 2,
      "relevance": 0.92
    },
    {
      "text": "Further discussion on this topic... [truncated]",
      "page": 5,
      "relevance": 0.87
    }
  ],
  "document_info": {
    "name": "document.pdf",
    "pages": 10,
    "chunks": 45
  }
}
```

**Response (Not Found - 200):**
```json
{
  "answer": "Not available in document",
  "sources": [],
  "document_info": {
    "name": "document.pdf",
    "pages": 10,
    "chunks": 45
  }
}
```

**Example:**
```bash
curl -X POST -H "Content-Type: application/json" \
  -d '{"question":"What is the main topic?"}' \
  http://localhost:8000/ask
```

---

### GET /document
Get information about the currently loaded document

**Response (With Document):**
```json
{
  "loaded": true,
  "document": {
    "name": "document.pdf",
    "pages": 10,
    "chunks": 45
  }
}
```

**Response (No Document):**
```json
{
  "loaded": false,
  "message": "No PDF loaded"
}
```

**Example:**
```bash
curl http://localhost:8000/document
```

---

### POST /clear
Clear the current document and message history

**Response:**
```json
{
  "success": true,
  "message": "Document cleared successfully"
}
```

**Example:**
```bash
curl -X POST http://localhost:8000/clear
```

---

### GET /health
Health check endpoint

**Response:**
```json
{
  "status": "healthy",
  "api_key_configured": true,
  "document_loaded": true
}
```

**Example:**
```bash
curl http://localhost:8000/health
```

---

### GET /
Root endpoint

**Response:**
```json
{
  "status": "ok",
  "message": "Chat with PDF API is running",
  "version": "1.0.0"
}
```

---

## Error Codes

| Code | Meaning | Solution |
|------|---------|----------|
| 400 | Bad Request | Check request format |
| 400 | File must be a PDF | Upload a PDF file |
| 400 | PDF has no pages | Use a valid PDF |
| 400 | Invalid PDF file | Ensure PDF isn't corrupted |
| 400 | No PDF uploaded | Upload a PDF first |
| 400 | Question cannot be empty | Provide a question |
| 500 | Internal Server Error | Check backend logs |

## Rate Limiting
Not currently implemented. Add for production:
```
- 10 uploads per minute
- 30 questions per minute
- Per IP or per user
```

## Request/Response Structure

### Question Request
```typescript
interface QuestionRequest {
  question: string;
}
```

### Answer Response
```typescript
interface AnswerResponse {
  answer: string;
  sources: Array<{
    text: string;
    page: number;
    relevance: number;
  }>;
  document_info?: {
    name: string;
    pages: number;
    chunks: number;
  };
}
```

## CORS
Enabled for all origins (modify for production)

## Content Types
- Request: `application/json` or `multipart/form-data`
- Response: `application/json`

## Interactive Documentation
Available at: http://localhost:8000/docs (Swagger UI)

## Usage Examples

### JavaScript/Fetch
```javascript
const response = await fetch('http://localhost:8000/ask', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ question: 'What is X?' })
});
const data = await response.json();
console.log(data.answer);
```

### Python/Requests
```python
import requests

response = requests.post(
    'http://localhost:8000/ask',
    json={'question': 'What is X?'}
)
print(response.json()['answer'])
```

### cURL
```bash
curl -X POST http://localhost:8000/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"What is X?"}'
```

## Best Practices

1. **Always check document status** before asking questions
2. **Handle "Not available in document" response** gracefully
3. **Cache responses** to reduce API calls
4. **Validate file size** before upload (max 50MB recommended)
5. **Show source citations** to users for transparency
6. **Implement exponential backoff** for retries
7. **Monitor API latency** (2-3 seconds expected)
