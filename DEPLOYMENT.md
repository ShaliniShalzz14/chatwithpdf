# Deployment Options

## Local Development (Current)
- Run backend and frontend separately
- Perfect for testing and development
- See QUICKSTART.md for setup

## Docker Deployment

### Option 1: Using Docker Compose

1. **Build and run:**
```bash
docker-compose up --build
```

2. **Access:**
- Frontend: http://localhost:3000
- Backend: http://localhost:8000

3. **Stop:**
```bash
docker-compose down
```

### Option 2: Individual Docker Containers

**Backend Dockerfile:**
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Frontend Dockerfile:**
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
CMD ["npm", "start"]
```

## Cloud Deployment

### Vercel (Frontend)
1. Push frontend to GitHub
2. Import repo in Vercel
3. Set environment variables
4. Deploy instantly

### Railway/Render/Heroku (Backend)
1. Containerize backend with Docker
2. Deploy container
3. Set OPENAI_API_KEY
4. Update NEXT_PUBLIC_API_URL in frontend

### Full Cloud Stack Example (Railway)
```yaml
services:
  - backend:
      image: ghcr.io/you/chat-pdf-backend
      envVar: OPENAI_API_KEY
  
  - frontend:
      image: ghcr.io/you/chat-pdf-frontend
      envVar: NEXT_PUBLIC_API_URL
```

## Production Checklist

- [ ] Use HTTPS/TLS
- [ ] Set up rate limiting
- [ ] Add API authentication
- [ ] Use PostgreSQL instead of in-memory
- [ ] Implement caching
- [ ] Set up logging and monitoring
- [ ] Add CI/CD pipeline
- [ ] SSL certificates
- [ ] Database backups
- [ ] CDN for static assets
- [ ] Health checks and alerts

See README.md for more details on production readiness.
