# Docker Compose Guide

## Table of Contents

1. [What is Docker Compose?](#1-what-is-docker-compose)
2. [Why Do We Need It?](#2-why-do-we-need-it)
3. [When to Use It](#3-when-to-use-it)
4. [How to Use It](#4-how-to-use-it)
5. [Example Compose File](#5-example-compose-file)

---

## 1. What is Docker Compose?

Docker Compose is a tool for defining and running applications made of multiple containers. You describe your services in a single YAML file, then start everything with one command.

---

## 2. Why Do We Need It?

- Simplifies running multi-container applications.
- Defines all services (backend, frontend, database) in one file.
- Makes setup consistent and easy to version-control.
- Reduces manual steps and mistakes.
- Great for local development, testing, and CI/CD pipelines.

---

## 3. When to Use It

- Your app has **multiple services** (e.g. web + database + cache).
- You want to **automate** container startup and linking.
- For **development and testing** environments.
- To **simulate production** locally.

---

## 4. How to Use It

1. Create a `docker-compose.yml` file in your project folder.
2. Define the services, images, volumes, ports, and environment variables.
3. Run these commands:

   ```bash
   docker compose up        # start all services
   docker compose up -d     # start in background (detached)
   docker compose down      # stop and remove containers
   ```

> Note: Newer Docker uses `docker compose` (with a space). Older versions used `docker-compose` (with a hyphen). Both do the same thing.

---

## 5. Example Compose File

A three-service stack: a frontend, a backend, and a Postgres database with a named volume for persistent data.

```yaml
version: "3.9"

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - DB_HOST=db
      - DB_PORT=5432
      - DB_USER=postgres
      - DB_PASS=example
    depends_on:
      - db

  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: example
      POSTGRES_DB: myapp
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

**What to notice:**

- `depends_on` controls startup order (db starts before backend, backend before frontend).
- The backend reaches the database using the service name `db` as the host.
- The `db_data` named volume keeps the database data even if the container is recreated.
