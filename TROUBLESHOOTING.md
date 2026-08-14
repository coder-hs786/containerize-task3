# Troubleshooting Documentation

## Task 3 - Docker Containerization

### Issue 1: Backend Could Not Connect to MongoDB

#### Error

The backend container started successfully on port 5000, but MongoDB connection failed with:

MongooseServerSelectionError: connect ECONNREFUSED ::1:27017

The application was trying to connect to:

localhost:27017

#### Root Cause

The backend and MongoDB were running in separate Docker containers.

Inside a Docker container, localhost refers to the same container, not another container. Therefore, the backend could not reach MongoDB using localhost.

#### Solution

The MongoDB connection was configured using the MongoDB container name instead of localhost.

The MongoDB URI was configured as:

mongodb://mongo-container:27017/ecommerce

The backend container was also connected to the ecommerce_network so that it could communicate with the MongoDB container.

#### Verification

MongoDB hostname resolution was verified from the backend container using:

docker exec backend-container getent hosts mongo-container

The backend logs were also checked using:

docker logs backend-container

After the correction, the backend successfully connected to MongoDB.

---

## Issue 2: Docker Container Name Conflict

### Error

When starting the backend container, Docker returned:

Conflict. The container name "/backend-container" is already in use.

### Root Cause

A container with the name backend-container already existed.

### Solution

The existing backend container was checked before recreating it. The container was removed and recreated with the required configuration.

Command used:

docker rm -f backend-container

The backend container was then started again with the required environment variables and network configuration.

---

## Issue 3: Docker Network Configuration

### Problem

The backend and MongoDB containers needed to communicate with each other through Docker networking.

### Solution

MongoDB was connected to:

ecommerce_network

The backend was connected to:

backend_network
ecommerce_network

This allowed the backend container to communicate with MongoDB using the Docker service/container name:

mongo-container

### Verification

The networks were verified using:

docker inspect backend-container

docker inspect mongo-container

---

## Final Verification

The following components were successfully verified:

- MongoDB container running
- Backend container running on port 5000
- Frontend container running
- Frontend accessible through the browser
- Backend connected to MongoDB
- Docker networks configured
- Backend running as non-root node user
- Frontend running as non-root nginx user

## Result

Task 3 Docker containerization and application deployment were completed successfully.
