# Frontend - ReactJS with ChakraUI

This directory contains the frontend of the application built with ReactJS and ChakraUI.

## Prerequisites

- Node.js (version 14.x or higher)
- npm (version 6.x or higher)

```markdown
# Frontend Deployment Guide

This deploy the frontend of a full-stack web application using Docker and React.

# Dockerfile for Frontend

Create a Dockerfile in the frontend directory to containerize the React frontend:

```dockerfile
# frontend/Dockerfile
# Use an official node image as the base image
FROM node:18-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Build the application
RUN npm run build

# Use an official nginx image to serve the build
FROM nginx:stable-alpine

# Copy the build output to the Nginx html directory
COPY --from=0 /app/build /usr/share/nginx/html

# Expose the port the app runs on
EXPOSE 80

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
```

# 1. Environment Variables

Ensure you configure any environment variables required by your frontend application in the `.env` file or directly in the Dockerfile.

# 2. Building and Running the Frontend

Build and start the frontend service using Docker Compose:

```bash
docker compose up --build -d frontend
```

# 3. Accessing the Frontend

Assess the frontend service on `http://localhost` once it starts running. 
Ensure your backend endpoints are correctly configured to be accessed by the frontend application.


# 4. **Configure API URL**:
Ensure the API URL is correctly set in the `.env` file.
