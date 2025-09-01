# Deployment Guide

## Quick Start: Railway (Recommended for beginners)

### 1. Push to GitHub
```bash
git add .
git commit -m "Initial commit"
git push origin main
```

### 2. Deploy on Railway
1. Go to [railway.app](https://railway.app)
2. Connect your GitHub account
3. Click "New Project" → "Deploy from GitHub repo"
4. Select your repository
5. Railway will automatically use `docker-compose.yml` (CPU mode)

### 3. Configure Environment Variables
In Railway dashboard, add these environment variables:
```
REDIS_HOST=redis://redis:6379/0
ENV=production
```

## Alternative: Render

### 1. Deploy on Render
1. Go to [render.com](https://render.com)
2. Connect your GitHub account
3. Click "New" → "Web Service"
4. Select your repository
5. Configure:
   - **Build Command**: `docker build --build-arg CPU_ONLY=true -t docling-api .`
   - **Start Command**: `docker-compose -f docker-compose.production.yml up`
   - **Environment**: Docker

### 2. Add Redis Service
1. In Render dashboard, click "New" → "Redis"
2. Note the Redis URL
3. Update environment variable: `REDIS_HOST=<your-redis-url>`

## VPS Deployment (DigitalOcean, Linode, etc.)

### 1. Server Setup
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Docker and Docker Compose
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. Deploy Application
```bash
# Clone your repository
git clone https://github.com/yourusername/your-repo.git
cd your-repo

# Create .env file
echo "REDIS_HOST=redis://redis:6379/0" > .env
echo "ENV=production" >> .env

# Start services
docker-compose -f docker-compose.production.yml up -d --scale celery_worker=2
```

### 3. Set up Reverse Proxy (Optional)
```bash
# Install Nginx
sudo apt install nginx -y

# Create Nginx config
sudo nano /etc/nginx/sites-available/docling-api
```

Add this configuration:
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /flower {
        proxy_pass http://localhost:5556;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/docling-api /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `REDIS_HOST` | Redis connection URL | `redis://redis:6379/0` |
| `ENV` | Environment mode | `production` |

## Scaling Options

### Horizontal Scaling (More Workers)
```bash
# Scale to 3 workers
docker-compose -f docker-compose.production.yml up -d --scale celery_worker=3
```

### Vertical Scaling (More Resources)
Update your hosting plan or VPS specifications for better performance.

## Monitoring

- **API Health**: `https://your-domain.com/docs`
- **Flower Dashboard**: `https://your-domain.com/flower`
- **Logs**: `docker-compose -f docker-compose.production.yml logs -f`

## Security Considerations

1. **Use HTTPS**: Set up SSL certificates (Let's Encrypt)
2. **Firewall**: Configure firewall rules
3. **Environment Variables**: Never commit sensitive data
4. **Regular Updates**: Keep Docker images updated

## Cost Optimization

- **Railway**: ~$5-20/month depending on usage
- **Render**: Free tier available, then ~$7/month
- **DigitalOcean**: ~$5-12/month for basic VPS
- **Google Cloud Run**: Pay-per-use, very cost-effective for low traffic
