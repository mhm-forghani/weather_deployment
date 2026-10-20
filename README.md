# Weather API — Production Deployment

This repository contains the Docker Compose configuration and deployment instructions for running Weather API in production. The application image is pulled from GitHub Container Registry (GHCR); the application source code is not required on the production server.

## Prerequisites

- Docker Engine and the Docker Compose plugin
- Access to the private GHCR package `ghcr.io/mhm-forghani/weather-api`
- A GitHub Personal Access Token with `read:packages` permission

## 1. Create the deployment directory

```bash
sudo mkdir -p /opt/weather-api
cd /opt/weather-api
```

Creates the directory used to store deployment files on the server.

## 2. Download the deployment files

Download `docker-compose.yaml` and `.env.prod.example` from this repository and place them in `/opt/weather-api`.

Create the production environment file:

```bash
cp .env.prod.example .env.prod
```

Creates the real environment file from the example template. Replace all placeholder values with the actual production settings.

Protect the environment file:

```bash
chmod 600 .env.prod
```

Restricts access to the environment file to its owner.

**Never commit `.env.prod` or publish its contents.**

## 3. Authenticate with GHCR

```bash
docker login ghcr.io -u YOUR_GITHUB_USERNAME
```

Authenticates Docker with GitHub Container Registry. When prompted, enter a Personal Access Token that has permission to read the private package.

## 4. Pull the application image

```bash
docker compose --env-file .env.prod pull
```

Downloads the images defined in the Compose configuration. The application image is pulled from GHCR.

## 5. Start the services

```bash
docker compose --env-file .env.prod up -d
```

Starts the API, PostgreSQL, Redis, Celery workers, and Celery Beat in the background.

## 6. Check service status

```bash
docker compose --env-file .env.prod ps
```

Displays the status of the containers.

To inspect the application logs:

```bash
docker compose --env-file .env.prod logs --tail=100 app
```

Displays the last 100 log lines from the API container.

## 7. Update the application

After a new application image has been published to GHCR, run:

```bash
docker compose --env-file .env.prod pull
```

Downloads the latest images referenced by the Compose configuration.

Then run:

```bash
docker compose --env-file .env.prod up -d
```

Recreates containers when their images or configuration have changed.

## Security notes

- Keep `.env.prod` private and restrict its file permissions.
- Do not publish database credentials, `SECRET_KEY`, access tokens, or other secrets.
- Ensure access to the private GHCR package is granted only to authorized users.
- Back up persistent PostgreSQL data before performing risky maintenance.
