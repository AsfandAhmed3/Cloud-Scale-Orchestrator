# Task 1 - Docker Containerization (Backend)

This task containerizes the backend API service.

## Build image

```bash
docker build -f backend/Dockerfile -t proshop-backend:task1 .
```

## Run container

```bash
docker run --name proshop-backend-task1 --env-file .env -p 5001:5000 -d proshop-backend:task1
```

## Verify

- Open: http://localhost:5001/api/products
- Expected: JSON response with products list

## Stop and remove container

```bash
docker stop proshop-backend-task1
docker rm proshop-backend-task1
```
