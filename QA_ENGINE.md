# PDF Processor - Technical Details

## Text Extraction Strategy

### Why PyPDF2?
- Simple and lightweight
- Works with most PDFs
- Maintains page information
- No external dependencies (like Poppler)

### Page Tracking
```python
pages_text = []
for page_num, page in enumerate(pdf_reader.pages, 1):
    text = page.extract_text()
    pages_text.append((text, page_num))  # Track page number
```

## Chunking Strategy

### Character-Based Chunking
```
Document: "The quick brown fox jumps over... [1000+ chars] ...lazy dog"
                ↓
Chunks of 1000 characters with 200 character overlap
                ↓
Chunk 1: chars 0-1000
Chunk 2: chars 800-1800 (200 char overlap)
Chunk 3: chars 1600-2600 (200 char overlap)
...
```

### Why Overlap?
- Prevents mid-sentence splits
- Ensures concepts aren't split across chunk boundaries
- Better semantic continuity

### Configuration
- Chunk size: 1000 characters (adjustable via env)
- Overlap: 200 characters
- Total chunks for typical PDF: 30-50

## Validation

### PDF Validation Checks
1. **File Type**: Confirm .pdf extension
2. **PDF Integrity**: Verify it can be parsed by PyPDF2
3. **Not Empty**: Ensure PDF has at least 1 page
4. **Text Extractable**: Validate text can be extracted

### Text Validation
- Strips empty pages
- Removes excessive whitespace
- Handles encoding issues

## Memory Efficiency

### Current Approach (Acceptable for single document)
- Entire PDF loaded into memory
- Text stored during chunking
- Embeddings generated on-demand

### For Production
- Stream PDF processing
- Database storage instead of memory
- Lazy loading of embeddings

## Example: Processing a 10-Page PDF

```
Input: article.pdf (5 MB, 10 pages)
       ↓
[Extraction]: ~500ms
- 10 pages × ~2000 chars = 20,000 chars total
       ↓
[Chunking]: ~100ms
- 20,000 chars ÷ 1000 = ~20 chunks
       ↓
[Embedding]: ~1000ms
- 20 chunks × 50ms per chunk = ~1000ms
       ↓
Output: 20 chunks with embeddings
       ↓
Ready for queries: ~1.6 seconds total
```

## Supported Features

✓ Standard PDFs (text-based)
✗ Scanned PDFs (would need OCR)
✗ PDF forms
✓ Multi-page documents
✓ Different encodings
✓ Large documents (tested up to 100 pages)
