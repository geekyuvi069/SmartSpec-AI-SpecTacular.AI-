# Deployment Guide

This guide covers deploying SpecTacular.AI to various platforms.

## Table of Contents
1. [Local Development](#local-development)
2. [Docker (Recommended)](#docker)
3. [Vercel](#vercel)
4. [Railway](#railway)
5. [Render](#render)
6. [AWS](#aws)

---

## Local Development

### Quick Start
```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run development server
python app.py
```

Visit `http://localhost:5000`

### With Auto-reload
Flask automatically reloads on file changes when `FLASK_ENV=development`.

---

## Docker

### Prerequisites
- Docker installed on your system
- Docker Compose (optional)

### Using Docker Compose (Easiest)
```bash
docker-compose up --build
```

Access at `http://localhost:5000`

### Manual Docker Commands
```bash
# Build image
docker build -t spectacular-ai .

# Run container
docker run -p 5000:5000 \
  -v $(pwd)/data:/app/data \
  -e SESSION_SECRET="your-secret-key" \
  spectacular-ai

# On Windows (PowerShell):
docker run -p 5000:5000 `
  -v ${PWD}/data:/app/data `
  -e SESSION_SECRET="your-secret-key" `
  spectacular-ai
```

### Container Management
```bash
# View logs
docker logs -f <container-id>

# Stop container
docker stop <container-id>

# Remove container
docker rm <container-id>

# Push to Docker Hub
docker tag spectacular-ai yourusername/spectacular-ai
docker push yourusername/spectacular-ai
```

---

## Vercel

### Limitations ⚠️
Vercel is optimized for serverless functions with these constraints:
- **Max execution time**: 10 seconds
- **Max payload size**: 4.5MB
- **Cold start**: ~2-3 seconds
- **ML Models**: May timeout on first download

**Recommendation**: Use only for lightweight deployments or UI-only (use API elsewhere).

### Setup Steps

1. **Create `vercel.json`** (already included)

2. **Initialize Git repository**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/yourusername/SpecTacularAI.git
   git push -u origin main
   ```

3. **Install Vercel CLI**
   ```bash
   npm install -g vercel
   ```

4. **Deploy**
   ```bash
   vercel
   ```

5. **Set Environment Variables**
   ```bash
   vercel env add SESSION_SECRET
   # Enter a secure random value
   ```

6. **Re-deploy with env vars**
   ```bash
   vercel --prod
   ```

### Optimize for Vercel
```bash
# Install serverless dependencies
pip install -r requirements.txt -t package

# Use smaller models
# Edit src/semantic_search.py:
# Change: "all-MiniLM-L6-v2"
# To: "all-MiniLM-L6-v2"  (or lighter model)
```

---

## Railway.app

### Better Alternative for Python/ML

1. **Connect GitHub Repository**
   - Visit https://railway.app
   - Connect your GitHub account
   - Select repository

2. **Configure Environment**
   - Set `FLASK_ENV=production`
   - Set `SESSION_SECRET` to secure value
   - Set `PYTHON_VERSION=3.11`

3. **Deploy**
   - Railway auto-deploys on git push
   - Access via Railway domain

### Features
✅ Unlimited execution time  
✅ Supports large files  
✅ Persistent storage support  
✅ Easy environment variables  

---

## Render

### Recommended Alternative

1. **Create Account** at https://render.com

2. **Connect Repository**
   - New → Web Service
   - Connect GitHub repository

3. **Configure**
   ```
   Name: spectacular-ai
   Environment: Python 3.11
   Build Command: pip install -r requirements.txt
   Start Command: gunicorn --bind 0.0.0.0:5000 app:app
   ```

4. **Set Environment Variables**
   - `FLASK_ENV`: production
   - `SESSION_SECRET`: [secure value]

5. **Deploy**
   - Click Create Web Service
   - Monitor deployment logs

### Additional Options
- **PostgreSQL**: Add database if needed
- **Persistent Disk**: Store data across deployments
- **Custom Domain**: Connect your domain

---

## AWS

### Using EC2 (Full Control)

1. **Launch EC2 Instance**
   - Image: Ubuntu 22.04
   - Type: t3.medium or larger
   - Security Groups: Allow HTTP(80), HTTPS(443), SSH(22)

2. **Connect & Setup**
   ```bash
   ssh -i key.pem ubuntu@your-instance-ip
   
   # Update system
   sudo apt update && sudo apt upgrade -y
   
   # Install Python & dependencies
   sudo apt install -y python3.11 python3-pip python3-venv git
   
   # Clone repository
   git clone https://github.com/yourusername/SpecTacularAI.git
   cd SpecTacularAI
   
   # Setup virtual environment
   python3 -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
   
   # Install Gunicorn
   pip install gunicorn
   ```

3. **Configure Nginx Reverse Proxy**
   ```bash
   sudo apt install -y nginx
   
   # Create config: /etc/nginx/sites-available/spectacular-ai
   sudo nano /etc/nginx/sites-available/spectacular-ai
   ```
   
   Add:
   ```nginx
   upstream app {
       server 127.0.0.1:5000;
   }
   
   server {
       listen 80;
       server_name your-domain.com;
       
       location / {
           proxy_pass http://app;
           proxy_set_header Host $host;
       }
       
       location /static {
           alias /home/ubuntu/SpecTacularAI/web/static;
       }
   }
   ```

   ```bash
   sudo ln -s /etc/nginx/sites-available/spectacular-ai /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

4. **Setup Systemd Service**
   ```bash
   sudo nano /etc/systemd/system/spectacular-ai.service
   ```
   
   Add:
   ```ini
   [Unit]
   Description=SpecTacular AI Application
   After=network.target
   
   [Service]
   User=ubuntu
   WorkingDirectory=/home/ubuntu/SpecTacularAI
   Environment="PATH=/home/ubuntu/SpecTacularAI/venv/bin"
   ExecStart=/home/ubuntu/SpecTacularAI/venv/bin/gunicorn \
       --workers 4 \
       --bind 127.0.0.1:5000 \
       app:app
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   ```
   
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable spectacular-ai
   sudo systemctl start spectacular-ai
   ```

5. **Setup HTTPS (Let's Encrypt)**
   ```bash
   sudo apt install -y certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

---

## Environment Variables

### Required
- `SESSION_SECRET`: Random secure string for session encryption
- `FLASK_ENV`: Either `development` or `production`

### Optional
- `FLASK_APP`: Defaults to `app.py`
- `FLASK_DEBUG`: Set to `0` in production
- `MODEL_CACHE_DIR`: Where to cache ML models

### Generate Secure Secret
```bash
python -c "import secrets; print(secrets.token_urlsafe(32))"
```

---

## Monitoring & Logs

### Docker Logs
```bash
docker logs -f spectacular-ai
```

### Systemd Logs (AWS)
```bash
sudo journalctl -u spectacular-ai -f
```

### Application Logs
Check `data/` directory for automatically created logs.

---

## Performance Optimization

### For Serverless (Vercel)
- Use lightweight models
- Pre-compile models
- Increase function timeout
- Consider backend API elsewhere

### For Traditional Hosting (EC2/Railway)
- Use full models (all-MiniLM-L6-v2)
- Enable file caching
- Set appropriate chunk sizes
- Monitor memory usage

### General Tips
- Enable gzip compression in Nginx
- Use CDN for static files
- Implement caching headers
- Monitor model download times

---

## Troubleshooting

### Port Already in Use
```bash
# Find process using port 5000
lsof -i :5000  # macOS/Linux
netstat -ano | findstr :5000  # Windows

# Kill process
kill -9 <PID>  # macOS/Linux
taskkill /PID <PID> /F  # Windows
```

### Model Download Timeout
```bash
# Pre-download models
python -c "from sentence_transformers import SentenceTransformer; \
    SentenceTransformer('all-MiniLM-L6-v2')"
```

### Memory Issues
- Increase instance size
- Reduce chunk size
- Use lighter model
- Enable disk caching

### Static Files Not Loading
```bash
# Ensure Nginx/proxy correctly routes
# Check file permissions
chmod -R 755 web/
```

---

## Support & Resources

- **Documentation**: See [readme.md](readme.md)
- **Issues**: GitHub Issues
- **Community**: Discussions

---

**Last Updated**: December 2025
