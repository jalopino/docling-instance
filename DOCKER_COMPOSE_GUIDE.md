# Docker Compose Files Explanation

## 📁 Available Docker Compose Files

### 1. `docker-compose.yml` (DEFAULT - CPU Mode)
- **Usage**: `docker compose up`
- **Mode**: CPU-only processing
- **Purpose**: Default configuration for hosting platforms
- **Best for**: Most hosting platforms, cost-effective deployment

### 2. `docker-compose.cpu.yml` (Explicit CPU Mode)
- **Usage**: `docker compose -f docker-compose.cpu.yml up`
- **Mode**: CPU-only processing
- **Purpose**: Explicit CPU configuration
- **Best for**: When you want to be explicit about CPU usage

### 3. `docker-compose.gpu.yml` (GPU Mode)
- **Usage**: `docker compose -f docker-compose.gpu.yml up`
- **Mode**: GPU-accelerated processing
- **Purpose**: High-performance processing
- **Best for**: When you have GPU access and need maximum speed

### 4. `docker-compose.production.yml` (Production CPU)
- **Usage**: `docker compose -f docker-compose.production.yml up`
- **Mode**: CPU-only processing with production optimizations
- **Purpose**: Production deployment with persistence
- **Best for**: Production servers with data persistence needs

## 🎯 How Docker Knows Which to Use

### Default Behavior
- **Without `-f` flag**: Uses `docker-compose.yml`
- **With `-f` flag**: Uses the specified file

### Examples
```bash
# Uses default (CPU mode)
docker compose up

# Explicitly use CPU mode
docker compose -f docker-compose.cpu.yml up

# Use GPU mode (requires GPU support)
docker compose -f docker-compose.gpu.yml up

# Use production CPU mode
docker compose -f docker-compose.production.yml up
```

## 🚀 For Hosting Platforms

Most hosting platforms (Railway, Render, etc.) will automatically use `docker-compose.yml` if no specific file is specified. Since we've set `docker-compose.yml` to use CPU mode, it will work perfectly for hosting.

## 🔧 Current Status

You are currently running: **CPU Mode** (`docker-compose.cpu.yml`)
- API: http://localhost:8080
- Flower Dashboard: http://localhost:5556

## 💡 Recommendation

For hosting, use the default `docker-compose.yml` file:
```bash
docker compose up --build --scale celery_worker=1
```

This will automatically use CPU mode and work on any hosting platform.
