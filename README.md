# Data Management Project

This repository contains the Docker-based environment for the university Data Management course. It starts an Oracle database instance using Docker Compose so you can work with a local database for assignments and experiments.

## Prerequisites

Before using the project, make sure you have:

- Docker installed
- Docker Compose installed
- A terminal with access to the project folder

## Quick start

1. Open a terminal in the project root.
2. Copy the example environment file:

   ```bash
   cp .env_example .env
   ```

3. Edit `.env` and set secure database passwords:

   ```env
   ORACLE_PASSWORD=your_strong_password
   APP_USER_PASSWORD=your_app_password
   ```

   These passwords are used when the database is created for the first time. Changing
   `.env` later will not change passwords in an existing database volume.

4. Start the database container:

   ```bash
   docker compose up -d
   ```

5. Wait until the container shows as `healthy` (the first startup can take a few minutes):

   ```bash
   docker compose ps
   ```

6. To stop the environment later:

   ```bash
   docker compose down
   ```

## Database details

The project creates an Oracle Free instance. In your database client, create an
Oracle connection with:

- host: localhost
- port: 1521
- service name: FREEPDB1
- username: student
- password: value from `APP_USER_PASSWORD`

For an administrator connection, use username `system` and the value of
`ORACLE_PASSWORD` as the password. Use the same host, port, and service name.

Once connected, you can check the connection with `SELECT 1 FROM dual;`.

## Useful commands

View logs:

```bash
docker compose logs -f oracle
```

Restart the service:

```bash
docker compose restart oracle
```

Start over with a new database and the current `.env` passwords (this deletes
all stored data):

```bash
docker compose down -v
```

## Notes

- The database data is persisted in the Docker volume `oracle-data`.
- The configuration is defined in `compose.yaml`.
- Keep your `.env` file local and do not commit real passwords to version control.

## Troubleshooting

If the container does not start correctly:

```bash
docker compose ps
docker compose logs oracle
```

Then check that Docker is running and your `.env` file contains valid values.
