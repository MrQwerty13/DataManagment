# Data Management Project

This project provides a local PostgreSQL 18 database for the university Data Management course. It runs PostgreSQL with Docker Compose and is intended for macOS with Docker Desktop.

## Requirements

- macOS
- Docker Desktop for Mac
- Docker Compose, included with Docker Desktop

Make sure Docker Desktop is running before using the commands below. Check it with:

```bash
docker info
```

## Setup

From the project directory, create a local environment file:

```bash
cp .env_example .env
```

Open `.env` and replace the example password with a strong local password:

```env
POSTGRES_PASSWORD=your_secure_password
```

The `.env` file is local configuration and must not be committed.

## Start the database

Start PostgreSQL in the background:

```bash
docker compose up -d
```

Check the container status:

```bash
docker compose ps
```

The database is ready when the `postgres` service reports `healthy`.

## Database connection

Use these settings in a PostgreSQL client or IDE:

| Setting | Value |
| --- | --- |
| Host | `localhost` |
| Port | `5432` |
| Database | `university` |
| User | `student` |
| Password | Value of `POSTGRES_PASSWORD` in `.env` |

For example, using the PostgreSQL command-line client:

```bash
psql "postgresql://student:your_secure_password@localhost:5432/university"
```

You can test the connection with:

```sql
SELECT 1;
```

## Useful commands

View PostgreSQL logs:

```bash
docker compose logs -f postgres
```

Restart the database:

```bash
docker compose restart postgres
```

Stop the container while preserving database data:

```bash
docker compose down
```

Stop the container and delete all database data:

```bash
docker compose down -v
```

## Data persistence

PostgreSQL data is stored in the Docker volume `postgres-data`. The data remains available after `docker compose down` and container restarts. Use `docker compose down -v` only when you intentionally want to reset the database.

## Troubleshooting

If Docker commands fail with an error about `docker.sock`, start Docker Desktop and wait until it reports that Docker is running:

```bash
open -a Docker
docker info
```

If the service is unhealthy, inspect its logs:

```bash
docker compose ps
docker compose logs postgres
```

If port `5432` is already in use, stop the other PostgreSQL instance or change the host-side port in `compose.yaml`, for example:

```yaml
ports:
  - "5433:5432"
```

Then connect to port `5433` from macOS. The PostgreSQL port inside the container remains `5432`.
