# Database Setup Instructions

## Problem
The application is running locally but cannot access the database because PostgreSQL is not running.

## Solution

### Option 1: Use Docker for Database Services (Recommended)

1. **Start the database services:**
   ```bash
   cd /home/astro/Documents/GitHub/mini-reg/mini-rag/docker
   sudo docker compose up -d pgvector qdrant redis rabbitmq
   ```

2. **Wait for services to start (about 30 seconds)**

3. **Verify services are running:**
   ```bash
   sudo docker compose ps
   ```

4. **Restart your FastAPI application** (it should auto-reload, but if needed):
   ```bash
   # Kill existing server
   pkill -f "uvicorn main:app"
   
   # Start it again
   cd /home/astro/Documents/GitHub/mini-reg/mini-rag/src
   /home/astro/.conda/envs/mini-rag/bin/uvicorn main:app --reload --host 0.0.0.0 --port 5000
   ```

### Option 2: Install PostgreSQL Locally

If you prefer to install PostgreSQL locally instead of using Docker:

1. **Install PostgreSQL:**
   ```bash
   # On Fedora/RHEL:
   sudo dnf install postgresql-server postgresql-contrib
   
   # Initialize and start:
   sudo postgresql-setup --initdb
   sudo systemctl enable postgresql
   sudo systemctl start postgresql
   ```

2. **Create database and user:**
   ```bash
   sudo -u postgres psql
   CREATE DATABASE minirag;
   CREATE USER postgres WITH PASSWORD 'minirag2222';
   ALTER USER postgres WITH SUPERUSER;
   \q
   ```

3. **Update .env file:**
   - Change `POSTGRES_PORT=5400` back to `POSTGRES_PORT=5432`

## Current Configuration

The `.env` file is currently configured for Docker:
- `POSTGRES_HOST=localhost`
- `POSTGRES_PORT=5400` (Docker mapped port)
- `POSTGRES_USERNAME=postgres`
- `POSTGRES_PASSWORD=minirag2222`
- `POSTGRES_MAIN_DATABASE=minirag`

## Ports Used by Docker Services

- PostgreSQL: `localhost:5400` (container port 5432)
- Qdrant: `localhost:6333` and `6334`
- Redis: `localhost:6379`
- RabbitMQ: `localhost:5672` (AMQP) and `15672` (Management UI)

## Troubleshooting

If you still can't connect:

1. **Check if containers are running:**
   ```bash
   sudo docker ps | grep -E "pgvector|qdrant|redis|rabbitmq"
   ```

2. **Check container logs:**
   ```bash
   sudo docker compose logs pgvector
   sudo docker compose logs qdrant
   ```

3. **Test PostgreSQL connection:**
   ```bash
   # Using docker exec
   sudo docker exec -it pgvector psql -U postgres -d minirag
   ```

