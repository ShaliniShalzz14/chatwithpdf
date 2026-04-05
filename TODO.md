# Task Progress: Fix Uvicorn SyntaxError

## Completed
- [x] Replace backend/qa_engine_fixed.py with clean sentence-transformers QAEngine code
- [ ] Test server start: cd backend && uvicorn main:app --reload (user to confirm)

## Next Steps
1. Install deps if missing: pip install sentence-transformers google-generativeai numpy
2. Set GEMINI_API_KEY in .env
3. Test PDF upload and Q&A
4. Update requirements.txt with new deps

Server should now start without syntax errors.
