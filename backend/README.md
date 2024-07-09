# Backend - FastAPI with PostgreSQL

This directory contains the backend of the application built with FastAPI and a PostgreSQL database.

## Prerequisites

- Python 3.8 or higher
- Poetry (for dependency management)
- PostgreSQL (ensure the database server is running)

#  Dockerfile for Backend

Create a Dockerfile in the backend directory to containerize the FastAPI backend:

```dockerfile
# backend/Dockerfile
FROM python:3.10-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y netcat

# Copy the dependencies files
COPY pyproject.toml poetry.lock ./

# Install Poetry
RUN pip install poetry

# Install dependencies
RUN poetry install --no-dev

# Copy the application code
COPY . .

# Expose the application port
EXPOSE 8000

# Command to run the application
CMD ["poetry", "run", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#  Environment Variables

Configure the `DATABASE_URL` environment variable in your `.env` file or directly in the `docker-compose.yml` 

#  Building and Running the Backend

Build and start the backend service using Docker Compose:

```bash
docker compose up --build -d backend
```

#  Accessing the Backend

Access your backend service on `http://localhost:8000` once it start running. 
Ensure your frontend application or API clients are configured to communicate with this endpoint.
