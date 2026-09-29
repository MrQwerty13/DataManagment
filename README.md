# Data Management Project

This project provides a local Microsoft SQL Server 2022 database for the university Data Management course. It runs in Docker Compose on macOS with Docker Desktop and on a Raspberry Pi with Docker Engine, and it is meant to be used from DataGrip on a desktop machine.

## Requirements

- macOS with Docker Desktop, or Raspberry Pi OS with Docker installed
- Docker Compose (included with Docker)

Make sure Docker is running before using the commands below. Check it with:

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
MSSQL_SA_PASSWORD=your_secure_password
```

SQL Server enforces strong passwords: at least 8 characters with uppercase, lowercase, digits and non-alphanumeric characters. The container exits if the password does not match the policy.

The `.env` file is local configuration and must not be committed.

## Start the database

Start SQL Server in the background:

```bash
docker compose up -d
```

Check the container status:

```bash
docker compose ps
```

The database is ready when the `sqlserver` service reports `healthy`. The first start pulls the image and can take a few minutes.

## Database connection

Use these settings in DataGrip or any other SQL client:

| Setting | Value |
| --- | --- |
| Host | `localhost` (on macOS) or the Raspberry Pi IP address (from the desktop) |
| Port | `1433` |
| Database | `master` |
| User | `sa` |
| Password | Value of `MSSQL_SA_PASSWORD` in `.env` |
| Driver | Microsoft SQL Server (trust server certificate: enabled) |

Test the connection with:

```sql
SELECT 1;
```

## Raspberry Pi deployment

The official SQL Server image is amd64-only, so on the ARM64 Raspberry Pi it runs under QEMU emulation. Register the emulator once on the Pi:

```bash
docker run --privileged --rm multiarch/qemu-user-static --reset -p yes
```

Then deploy the same files as on macOS:

```bash
git pull
cp .env_example .env   # set MSSQL_SA_PASSWORD
docker compose up -d
docker compose ps      # wait until healthy
```

The container memory limit is capped with `MSSQL_MEMORY_LIMIT_MB=1024` in `compose.yaml` so the 2 GB Raspberry Pi 4B has headroom for the OS. Expect slower startup and queries under emulation.

Connect DataGrip on the desktop to `PI_IP_ADDRESS:1433` as `sa`.

## Useful commands

View SQL Server logs:

```bash
docker compose logs -f sqlserver
```

Open a SQL shell inside the container:

```bash
docker exec -it university-sqlserver /opt/mssql-tools18/bin/sqlcmd \
  -S localhost -U sa -P "$MSSQL_SA_PASSWORD" -C
```

Restart the database:

```bash
docker compose restart sqlserver
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

SQL Server data is stored in the Docker volume `sqlserver-data`. The data remains available after `docker compose down` and container restarts. Use `docker compose down -v` only when you intentionally want to reset the database.

## Troubleshooting

If Docker commands fail with an error about `docker.sock`, start Docker and wait until it reports that Docker is running:

```bash
open -a Docker
docker info
```

If the service is unhealthy, inspect its logs:

```bash
docker compose ps
docker compose logs sqlserver
```

If the container exits immediately with a password policy or `Password validation failed` error, pick a stronger `MSSQL_SA_PASSWORD` in `.env` and recreate the container:

```bash
docker compose up -d --force-recreate
```

If port `1433` is already in use, stop the other SQL Server instance or change the host-side port in `compose.yaml`, for example:

```yaml
ports:
  - "1444:1433"
```

Then connect to port `1444`. The SQL Server port inside the container remains `1433`.

If the container never becomes healthy on the Raspberry Pi, confirm the emulator is registered (`docker run --privileged --rm multiarch/qemu-user-static --reset -p yes`) and give it more time: emulated startup can take several minutes.
