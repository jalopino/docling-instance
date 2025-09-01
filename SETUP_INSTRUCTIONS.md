# Docling API Setup Instructions

## Overview
This is a Docker Compose setup for the Docling API, which converts various document formats (PDF, DOCX, PPTX, HTML, images, etc.) into Markdown format.

## Services Running

The following services are currently running:

1. **API Server** (`marker-api-cpu`)
   - Port: `8080`
   - URL: http://localhost:8080
   - API Documentation: http://localhost:8080/docs

2. **Redis** (`redis`)
   - Port: `6379`
   - Used as message broker and result backend for Celery tasks

3. **Celery Worker** (`docling-celery_worker-1`)
   - Processes document conversion tasks
   - Running in CPU mode

4. **Flower Dashboard** (`flower_cpu`)
   - Port: `5556`
   - URL: http://localhost:5556
   - Monitoring dashboard for Celery tasks and workers

## Usage

### Synchronous Document Conversion
```bash
curl -X POST "http://localhost:8080/documents/convert" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "document=@/path/to/your/document.pdf"
```

### Asynchronous Document Conversion
```bash
# Submit a document for conversion
curl -X POST "http://localhost:8080/conversion-jobs" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "document=@/path/to/your/document.pdf"

# Check conversion status (replace {job_id} with actual job ID)
curl -X GET "http://localhost:8080/conversion-jobs/{job_id}" \
  -H "accept: application/json"
```

### Batch Processing
```bash
curl -X POST "http://localhost:8080/batch-conversion-jobs" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "documents=@/path/to/document1.pdf" \
  -F "documents=@/path/to/document2.pdf"
```

## Configuration Options

- `image_resolution_scale`: Control the resolution of extracted images (1-4)
- `extract_tables_as_images`: Extract tables as images (true/false)

## Management Commands

### Start Services
```bash
docker compose -f docker-compose.cpu.yml up --build --scale celery_worker=1
```

### Stop Services
```bash
docker compose -f docker-compose.cpu.yml down
```

### View Logs
```bash
docker compose -f docker-compose.cpu.yml logs
```

### Scale Workers
```bash
# Scale to 3 workers
docker compose -f docker-compose.cpu.yml up --scale celery_worker=3
```

## Supported Document Formats

- PDF files
- Microsoft Word documents (DOCX)
- PowerPoint presentations (PPTX)
- HTML files
- Images (JPG, PNG, TIFF, BMP)
- AsciiDoc files
- Markdown files
- CSV files

## Features

- Text extraction and formatting
- Table detection, extraction and conversion
- Image extraction and processing
- Multi-language OCR support (French, German, Spanish, English, Italian, Portuguese)
- Configurable image resolution scaling
- Both synchronous and asynchronous processing
- Batch processing capabilities

## Monitoring

Access the Flower dashboard at http://localhost:5556 to monitor:
- Task status and progress
- Worker performance
- Success/failure rates
- Resource usage
- Task queues
