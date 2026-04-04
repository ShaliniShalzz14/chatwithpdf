# Chat with PDF - Quick Start Guide

## 🚀 Fastest Way to Get Started (5 minutes)

### 1. Get Your OpenAI API Key
- Visit https://platform.openai.com/api-keys
- Create a new secret key
- Copy it (you'll need it next)

### 2. Setup Backend

**Windows PowerShell:**
```powershell
cd backend
python -m venv venv
.\venv\Scripts\activate
pip install -r requirements.txt

# Create .env file with your key
$content = "OPENAI_API_KEY=sk-YOUR_KEY_HERE`nOPENAI_MODEL=gpt-4-turbo"
Set-Content -Path .env -Value $content

python main.py
```

**macOS/Linux Bash:**
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

cat > .env << EOF
OPENAI_API_KEY=sk-YOUR_KEY_HERE
OPENAI_MODEL=gpt-4-turbo
EOF

python main.py
```

### 3. Setup Frontend (New Terminal)

**Windows:**
```powershell
cd frontend
npm install
npm run dev
```

**macOS/Linux:**
```bash
cd frontend
npm install
npm run dev
```

### 4. Open Browser
Visit: http://localhost:3000

### 5. Try It Out!
1. Click the upload area and select a PDF
2. Wait for success message
3. Ask a question like "Summarize the document"
4. See the answer with page references!

---

## ⚡ Quick Troubleshooting

| Issue | Solution |
|-------|----------|
| "OPENAI_API_KEY not found" | Make sure .env exists in backend folder with your key |
| "Cannot connect to server" | Check backend is running on terminal 1 at http://localhost:8000 |
| "npm: command not found" | Install Node.js from https://nodejs.org |
| "python: command not found" | Install Python from https://python.org |
| "Invalid PDF" | Make sure PDF is valid and not corrupted |

---

## 📚 Useful Commands

**View API docs:**
```
http://localhost:8000/docs
```

**Check API health:**
```
curl http://localhost:8000/health
```

**Stop servers:**
```
Ctrl+C in both terminals
```

---

## 🎨 Development Mode

Both frontend and backend support hot-reload:
- Backend: Changes to .py files auto-restart
- Frontend: Changes to .tsx/.css files auto-refresh

Just save and watch it update!

---

Happy chatting! 🚀
