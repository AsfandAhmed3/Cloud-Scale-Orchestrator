# Task 2 - Multi-Container Setup using Docker Compose

This task runs 3 services together:
- Frontend
- Backend
- MongoDB

## Start all services

```bash
docker compose up --build -d
```

## Check running containers

```bash
docker compose ps
```

## Verify

- Frontend: http://localhost:3001
- Backend API: http://localhost:5002/api/products
- MongoDB host port: `27018`

## Stop services

```bash
docker compose down
```

## Notes

- Compose automatically creates a shared network.
- Backend talks to MongoDB using service name `mongo`:
  `mongodb://mongo:27017/proshop`
