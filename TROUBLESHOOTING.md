# Troubleshooting Guide

## Common Issues & Solutions

### 1. Backend Issues

#### Error: "OPENAI_API_KEY not found"
```
❌ Error: OPENAI_API_KEY not configured
```

**Solutions**:
1. Create `.env` file in backend directory
2. Add your API key: `OPENAI_API_KEY=sk-...`
3. Verify file is in correct location: `backend/.env`
4. Don't add quotes around the key
5. Restart the backend server

**Verify**:
```bash
cd backend
cat .env  # Should show your key
echo $OPENAI_API_KEY  # Should print it
```

---

#### Error: "Cannot find module 'PyPDF2'"
```
❌ ModuleNotFoundError: No module named 'PyPDF2'
```

**Solutions**:
1. Activate virtual environment:
   ```bash
   # Windows:
   venv\Scripts\activate
   
   # macOS/Linux:
   source venv/bin/activate
   ```
2. Install requirements: `pip install -r requirements.txt`
3. Verify installation: `pip list | grep PyPDF2`

---

#### Error: "Port 8000 is already in use"
```
❌ Address already in use: ('0.0.0.0', 8000)
```

**Solutions**:
1. **Kill existing process**:
   ```bash
   # Windows PowerShell:
   Get-Process python | Stop-Process
   
   # macOS/Linux:
   lsof -i :8000
   kill -9 <PID>
   ```
2. **Use different port**:
   ```bash
   uvicorn main:app --port 8001
   ```
3. **Check what's using the port**:
   ```bash
   netstat -ano | findstr 8000
   ```

---

#### Error: "Invalid PDF file"
```
❌ Invalid PDF file: Error message
```

**Solutions**:
1. Verify PDF is not corrupted:
   - Open in Adobe Reader or another PDF viewer
   - Try opening in command line: `file document.pdf`
2. Ensure PDF is not password protected:
   - Try opening without password in PDF viewer
3. Check file size:
   - Should be < 50MB
   - If > 100MB, might have extraction issues
4. Try re-exporting PDF:
   - Export from original source as new PDF

---

#### Error: "Could not extract text from PDF"
```
❌ Could not extract text from PDF
```

**Possible Causes**:
- Scanned PDF without OCR
- PDF is image-based
- PDF encoding issues

**Solutions**:
1. Use a text-based PDF (most PDFs are fine)
2. For scanned PDFs, use OCR first (not supported currently)
3. Try converting to PDF in different tool

---

#### API returns 500 errors
```
❌ Internal Server Error
```

**Debugging**:
1. Check backend terminal for error message
2. Verify OpenAI API key is valid
3. Check OpenAI account has credits
4. Try health check: `curl http://localhost:8000/health`

---

### 2. Frontend Issues

#### Error: "Cannot reach API"
```
❌ Failed to connect to backend
```

**Solutions**:
1. Verify backend is running:
   ```bash
   curl http://localhost:8000/health
   ```
2. Check API URL in `.env.local`:
   ```
   NEXT_PUBLIC_API_URL=http://localhost:8000
   ```
3. Verify backend is on same machine (or adjust URL)
4. Check firewall isn't blocking port 8000

---

#### Error: "npm: command not found"
```
❌ npm: command not found
```

**Solutions**:
1. Install Node.js from https://nodejs.org
2. Verify installation: `node --version`
3. Use correct npm path if custom installation
4. Restart terminal after installation

---

#### Port 3000 already in use
```
❌ Port 3000 is already in use
```

**Solutions**:
```bash
# Windows:
Get-Process node | Stop-Process

# macOS/Linux:
lsof -i :3000
kill -9 <PID>

# Or use different port:
npm run dev -- -p 3001
```

---

#### Styles not loading/CSS broken
```
❌ Page looks unstyled or broken
```

**Solutions**:
1. Clear browser cache: `Ctrl+Shift+Delete`
2. Rebuild frontend: `npm run build`
3. Restart dev server: Stop and `npm run dev`
4. Check `globals.css` exists in `app/` folder

---

#### Components not updating after upload
```
❌ Chat interface not appearing after PDF upload
```

**Solutions**:
1. Check browser console for errors: `F12`
2. Verify API response is successful
3. Check Redux/Zustand state: Browser DevTools
4. Try hard refresh: `Ctrl+Shift+R`

---

### 3. Full Stack Issues

#### Frontend + Backend working but no answer
```
❌ Upload succeeds, but questions return errors
```

**Debugging Steps**:
1. Check if OpenAI API is down: https://status.openai.com
2. Verify API key has valid credits
3. Check API key isn't rate limited
4. Look at backend logs for API errors
5. Test API directly:
   ```bash
   curl -X POST http://localhost:8000/ask \
     -H "Content-Type: application/json" \
     -d '{"question":"test"}'
   ```

---

#### "Not available in document" for valid questions
```
❌ Answer says "Not available" even though document has info
```

**Possible Causes**:
- Chunks aren't semantically similar to query
- Query is too different from document wording
- Document content is in a table or unusual format

**Solutions**:
1. Try rephrasing question with document's terminology
2. Increase `CHUNK_SIZE` in `.env` (more context)
3. Check actual document text contains the answer
4. Try simpler questions first

---

#### Slow responses (>5 seconds)
```
❌ Questions take a long time to answer
```

**Causes**:
- OpenAI API latency (1-2 seconds is normal)
- Network issues
- Large PDF with many chunks

**Solutions**:
1. Check internet speed
2. Verify OpenAI isn't overloaded
3. Reduce PDF size
4. Accept 2-3 seconds as normal

---

### 4. Environment Issues

#### "python: command not found"
```
❌ python: command not found
```

**Solutions**:
1. Install Python from https://python.org
2. On macOS, use `python3` instead of `python`
3. Add Python to PATH
4. Verify: `python --version`

---

#### Virtual environment activation failed
```
❌ (venv) not showing in prompt
```

**Solutions**:
```bash
# Windows PowerShell - may need to change execution policy:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# Then try:
venv\Scripts\activate

# Alternative (cmd.exe):
venv\Scripts\activate.bat
```

---

#### Requirements installation fails
```
❌ pip install -r requirements.txt fails
```

**Solutions**:
1. Upgrade pip: `pip install --upgrade pip`
2. Try installing one by one to find problematic package
3. Check Python version: `python --version`
4. On some systems: `pip3` instead of `pip`

---

### 5. Deployment Issues

#### Docker build fails
```
❌ Docker build ERROR
```

**Solutions**:
1. Verify Docker is running
2. Check Dockerfile path is correct
3. Build with verbose output: `docker build --verbose`
4. Check Dockerfile syntax

---

#### Can't access deployed app
```
❌ 404 or Connection refused
```

**Solutions**:
1. Verify app is running: `docker ps`
2. Check port mapping: `-p 3000:3000`
3. Verify environment variables are set
4. Check logs: `docker logs <container_id>`

---

### 6. Getting Help

**If issue isn't listed**:

1. **Check logs**:
   - Backend: Terminal output
   - Frontend: Browser console (F12)
   - API: http://localhost:8000/docs

2. **Test components individually**:
   ```bash
   # Test backend alone:
   curl http://localhost:8000/health
   
   # Test frontend alone:
   npm run build  # Check for build errors
   ```

3. **Reset environment**:
   ```bash
   # Backend
   rm -rf venv
   python -m venv venv
   pip install -r requirements.txt
   
   # Frontend
   rm -rf node_modules package-lock.json
   npm install
   ```

4. **Check documentation**:
   - README.md - Setup and overview
   - QUICKSTART.md - Fast setup
   - API_REFERENCE.md - API details
   - ARCHITECTURE.md - System design

---

## Quick Health Checks

**Is backend running?**
```bash
curl http://localhost:8000/health
# Should return: {"status": "healthy", ...}
```

**Is frontend running?**
```bash
Open http://localhost:3000 in browser
# Should load the app
```

**Is OpenAI API working?**
```bash
# In Python:
import openai
openai.api_key = "your_key"
print(openai.Model.list())
```

**Is PDF valid?**
```bash
# Try opening in PDF viewer
# Or: file document.pdf
```

---

## Emergency Reset

If everything is broken:

```bash
# Kill all running services
Ctrl+C in both terminals

# Reset backend
cd backend
rm -rf venv __pycache__ *.pyc
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate
pip install -r requirements.txt
python main.py

# Reset frontend (new terminal)
cd frontend
rm -rf node_modules .next
npm install
npm run dev
```

---

**Last Updated**: April 2026
**Still Having Issues?** Check the code comments or refer to the main README.md
