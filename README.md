# Full-Stack FastAPI and React Template
# Getting Started
This `README.md` file shows how to deploy the full-stack web application using Docker and Nginx Proxy Manager:

```markdown
# Full Stack Web Application Deployment

This is a full-stack web application with a React frontend and a FastAPI + PostgreSQL backend. The application is containerized using Docker and uses Nginx Proxy Manager to serve both the frontend and backend on the same port.

# Prerequisites

Docker
Docker Compose
An AWS EC2 instance for deployment
A domain or a free subdomain from Afraid DNS

# Instructions

# 1. Fork the Repository

Create a fork of this repository to add the necessary Docker and proxy configuration files.

# 2. Dockerization

Write Dockerfiles to containerize both the React frontend and FastAPI backend.

# Dockerfile for Frontend

Create a `Dockerfile` in the `frontend` directory:

```Dockerfile
# frontend/Dockerfile
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

# Dockerfile for Backend

Create a `Dockerfile` in the `backend` directory:

```Dockerfile
# backend/Dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY pyproject.toml poetry.lock ./
RUN pip install poetry
RUN poetry install --no-dev
COPY . .
EXPOSE 8000
CMD ["poetry", "run", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

# 3. Docker Compose Configuration

Create a `docker-compose.yml` file in the root directory:

```yaml
version: "3.8"
services:
  nginx-proxy-manager:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    depends_on:
      - backend
    ports:
      - "5173:80"

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/dbname
    depends_on:
      - db
    ports:
      - "8000:8000"

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: dbname
    volumes:
      - postgres_data:/var/lib/postgresql/data

  adminer:
    image: adminer
    ports:
      - "8080:8080"

volumes:
  postgres_data:
```

# 4. Configuration for Nginx Proxy Manager

Configure Nginx Proxy Manager to:
- Serve the frontend on `/`
- Proxy `/api`, `/docs`, and `/redoc` to the backend

# 5. Cloud Deployment

Deploy the application to an AWS EC2 instance.

# Steps:

1. Launch an EC2 instance and SSH into it.
2. Install Docker and Docker Compose on the instance.
3. Clone the forked repository to the instance.
4. Update the environment variables in the `docker-compose.yml` file as needed.
5. Run the following command to build and start the containers:

```sh
docker compose up --build -d
```

# 6. Domain and HTTPS Setup

Set up a domain or get a free subdomain from Afraid DNS. Configure HTTP to redirect to HTTPS and www to redirect to non-www using Nginx Proxy Manager.

# 7. Adminer and Proxy Manager Setup

- Configure Adminer to be accessible via `db.yourdomain.com`.
- Configure Nginx Proxy Manager to be accessible via `proxy.yourdomain.com`.
