# Data Management Project

This repository provides a local Microsoft SQL Server 2022 environment for the university Data Management course. The current Docker setup is defined in `compose.yaml` and runs a single SQL Server container for database work and assignments.

## Prerequisites

Before starting the environment, make sure you have:

- Docker installed
- Docker Compose available in your terminal
- A terminal opened in the project root

## Quick start

1. Copy the example environment file:

   ```bash
   cp .env_example .env
   ```

2. Edit `.env` and set a strong password for the SQL Server administrator account:

   ```env
   MSSQL_SA_PASSWORD=YourStrongPassword123!
   APP_USER_PASSWORD=write_password_two
   ```

3. Start the container:

   ```bash
   docker compose up -d
   ```

4. Check that the service is healthy:

   ```bash
   docker compose ps
   ```

5. When you are done, stop the environment:

   ```bash
   docker compose down
   ```

## Current database setup

The repository currently starts:

- Microsoft SQL Server 2022
- Container name: `university-sqlserver`
- Host: `localhost`
- Port: `1433`
- Database engine: SQL Server
- Data volume: `sqlserver-data`

The service is configured with:

- username: `sa`
- password: value from `MSSQL_SA_PASSWORD`

The container includes a platform override for Apple Silicon machines:

```yaml
platform: linux/amd64
```

This is included so the SQL Server image runs correctly on Macs with Apple Silicon (including M-series chips).

## Example connection

In a SQL Server client or IDE, connect with:

- Server: `localhost,1433`
- Authentication: SQL Server Authentication
- Username: `sa`
- Password: the value from `MSSQL_SA_PASSWORD`

You can test the connection with:

```sql
SELECT 1;
```

## Useful commands

View container logs:

```bash
docker compose logs -f sqlserver
```

Restart the container:

```bash
docker compose restart sqlserver
```

Remove the database and its stored data volume:

```bash
docker compose down -v
```

## Notes

- The database data is persisted in the Docker volume `sqlserver-data`.
- The service definition is in `compose.yaml`.
- Keep `.env` local and do not commit real passwords to version control.
- `APP_USER_PASSWORD` is included in `.env_example`, but the current Docker configuration does not automatically create an application user. You can use it when creating a user manually in SQL Server for your course project.

## Troubleshooting

If the container does not start or becomes unhealthy, run:

```bash
docker compose ps
docker compose logs sqlserver
```

Then verify that:

- Docker is running
- `.env` exists and contains `MSSQL_SA_PASSWORD`
- The required port `1433` is available on your machine
- You are using the correct architecture setting for Apple Silicon systems
