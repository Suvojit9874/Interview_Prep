# Docker Commands Reference

A complete list of the Docker commands you use every day, grouped by category. Good for quick revision before an interview.

## Table of Contents

1. [Docker CLI Commands](#1-docker-cli-commands)
2. [Docker Networking Commands](#2-docker-networking-commands)
3. [Dockerfile Instructions](#3-dockerfile-instructions)
4. [Docker Compose Commands](#4-docker-compose-commands)
5. [Optimizing Docker Images and Builds](#5-optimizing-docker-images-and-builds)
6. [Managing Multi-Container Apps with Compose](#6-managing-multi-container-apps-with-compose)
7. [Managing Environment Variables in Compose](#7-managing-environment-variables-in-compose)

---

## 1. Docker CLI Commands

These are the core commands to run and manage containers and images.

| Command | What it does |
| :-- | :-- |
| `docker run <image>` | Run an image as a container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (running and stopped) |
| `docker images` | List images on your machine |
| `docker pull <image>` | Download an image from a registry |
| `docker push <image>` | Upload an image to a registry |
| `docker stop <container>` | Stop a running container |
| `docker start <container>` | Start a stopped container |
| `docker restart <container>` | Restart a container |
| `docker rm <container>` | Remove a stopped container |
| `docker rm -f <container>` | Force remove a container (even if running) |
| `docker rmi <image>` | Remove an image |
| `docker exec -it <container> <cmd>` | Run a command inside a running container (e.g. a shell) |
| `docker logs -f <container>` | View and follow container logs |
| `docker inspect <container/image>` | Show low-level details |
| `docker build -t <name:tag> <path>` | Build an image from a Dockerfile |
| `docker network ls` | List networks |
| `docker volume ls` | List volumes |
| `docker login` / `docker logout` | Sign in or out of a registry |
| `docker run -v $(pwd):/app <image>` | Run a container and mount the current folder at `/app` |
| `docker system prune` | Clean up unused images, containers, volumes, and networks |
| `docker attach <container>` | Connect your terminal to a running container |
| `docker kill <container>` | Stop a container immediately (sends SIGKILL) |
| `docker stop $(docker ps -q)` | Stop all running containers |
| `docker container prune` | Remove all stopped containers |
| `docker cp <container>:/path ./` | Copy a file or folder from a container to your machine |

---

## 2. Docker Networking Commands

Used to create and manage the networks containers use to talk to each other.

| Command | What it does |
| :-- | :-- |
| `docker network` | Show network subcommands |
| `docker network ls` | List all networks |
| `docker network create <name>` | Create a new user-defined network |
| `docker network create --driver <driver> <name>` | Create a network with a specific driver (bridge, overlay, macvlan) |
| `docker network inspect <name>` | Show details of a network and its connected containers |
| `docker network connect <name> <container>` | Connect a running container to a network |
| `docker network connect --ip <address> <name> <container>` | Connect and assign a fixed IP |
| `docker network disconnect <name> <container>` | Disconnect a container from a network |
| `docker network rm <name>` | Remove one or more networks |
| `docker network prune` | Remove all unused networks |
| `docker run --network <name> <image>` | Run a container attached to a specific network |

> See `docker_02.md` for a full explanation of network types and when to use each.

---

## 3. Dockerfile Instructions

A Dockerfile is a script of instructions used to build an image.

| Instruction | What it does |
| :-- | :-- |
| `FROM` | Set the base image |
| `LABEL` | Add metadata to the image (replaces the old `MAINTAINER`) |
| `RUN` | Run a command during the build (e.g. install software) |
| `CMD` | Default command when the container starts (can be overridden) |
| `ENTRYPOINT` | Main executable of the container (not easily overridden) |
| `COPY` | Copy files from host into the image |
| `ADD` | Like `COPY`, but can also fetch URLs and extract archives |
| `WORKDIR` | Set the working directory |
| `ENV` | Set an environment variable |
| `EXPOSE` | Document which ports the container listens on |
| `USER` | Set the user to run as |
| `ARG` | Define a build-time variable |
| `VOLUME` | Create a mount point for external data |
| `ONBUILD` | Add a trigger that runs when this image is used as a base |
| `HEALTHCHECK` | Define how to check if the container is healthy |

**CMD vs ENTRYPOINT (common interview question):**
`ENTRYPOINT` sets the command that always runs. `CMD` provides default arguments that can be replaced at runtime. Used together, `ENTRYPOINT` is the executable and `CMD` is the default argument.

---

## 4. Docker Compose Commands

Compose manages multi-container applications defined in a YAML file.

| Command | What it does |
| :-- | :-- |
| `docker compose up` | Start all services in the Compose file |
| `docker compose up -d` | Start in detached (background) mode |
| `docker compose down` | Stop and remove services and containers |
| `docker compose build` | Build or rebuild service images |
| `docker compose ps` | List containers |
| `docker compose logs` | View service logs |
| `docker compose stop` / `start` | Stop or start existing services |
| `docker compose restart` | Restart services |
| `docker compose exec <service> <cmd>` | Run a command inside a running service |
| `docker compose rm` | Remove stopped service containers |
| `docker compose images` | List images used by the services |
| `docker compose config` | Validate and view the merged Compose file |
| `docker compose pull` / `push` | Pull or push service images |
| `docker compose create` | Create services without starting them |
| `docker compose cp` | Copy files to or from a container |
| `docker compose version` | Show the Compose version |

> See `docker_03.md` for a full Compose guide with examples.

---

## 5. Optimizing Docker Images and Builds

The goal is smaller images, faster builds, and better security.

1. **Use a minimal base image.** Start from `alpine` or `distroless` instead of a full `ubuntu`. This can save hundreds of megabytes.

2. **Use multi-stage builds.** Keep the build tools in one stage and copy only the final artifact into a small runtime image.

   ```dockerfile
   # Build stage
   FROM golang:1.21 AS builder
   WORKDIR /src
   COPY . .
   RUN go build -o app

   # Production stage
   FROM alpine:latest
   COPY --from=builder /src/app /app
   CMD ["/app"]
   ```

3. **Reduce the number of layers.** Each `RUN`, `COPY`, and `ADD` creates a layer. Combine related commands with `&&`.

   ```dockerfile
   RUN apt-get update && apt-get install -y package1 package2 \
       && apt-get clean && rm -rf /var/lib/apt/lists/*
   ```

4. **Clean up unnecessary files.** Remove caches and temporary files in the same layer you create them.

5. **Use a `.dockerignore` file.** Stop Docker from copying files you don't need (like `node_modules`, `.git`) into the build context.

   ```
   node_modules
   .git
   *.md
   Dockerfile*
   dist/
   ```

6. **Order commands for caching.** Copy dependency files and install dependencies *before* copying the source code. Docker can then reuse the cached dependency layer when only the code changes.

7. **Install only what you need.** Avoid extra tools. Use `--no-install-recommends` with `apt-get`.

8. **Use helper tools.** `Dive` (inspect layers), `Docker Slim` (shrink images), and `Hadolint` (Dockerfile linter).

**Benefits:** faster deployments, smaller attack surface, less storage and bandwidth use, and more consistent images.

---

## 6. Managing Multi-Container Apps with Compose

Docker Compose lets you define and run a full stack (web app, database, cache) from one YAML file.

**Key benefits**

- **Centralized config:** all services, networks, and volumes in one file.
- **One-command lifecycle:** `up`, `down`, and `ps` control the whole stack.
- **Automatic networking:** Compose creates a network so containers find each other by name.
- **Service dependencies:** control startup order with `depends_on` and `healthcheck`.
- **Persistent data:** use named volumes so data survives restarts.

**Core workflow**

```yaml
version: '3'
services:
  web:
    build: ./web
    ports:
      - "5000:5000"
    depends_on:
      - db
  db:
    image: postgres:alpine
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

**Common lifecycle commands**

- Start in background: `docker compose up -d`
- Check status: `docker compose ps`
- Stop and remove: `docker compose down`
- Scale a service: `docker compose up --scale web=3 -d`
- Follow logs: `docker compose logs -f`

**Best practices**

- Keep each service focused on one job.
- Use named volumes for databases to avoid data loss.
- Put config in environment variables and `.env` files.
- Define custom networks when you need isolation between stacks.
- Use `depends_on` with health checks for reliable startup order.
- Commit Compose files to version control.
- Build images locally for development; use prebuilt, tested images in production.

---

## 7. Managing Environment Variables in Compose

There are several ways to pass configuration into containers. Pick the one that fits.

**1. The `environment` attribute** (directly in the Compose file):

```yaml
services:
  webapp:
    environment:
      - DEBUG=true          # list style
      - API_KEY=abcdefg
```

You can also pull values from the shell or a `.env` file:

```yaml
services:
  webapp:
    environment:
      DEBUG: "${DEBUG}"
```

**2. An external `.env` file** (auto-loaded from the same folder):

```
DEBUG=true
API_KEY=yourapikeyvalue
WEB_PORT=8080
```

```yaml
services:
  webapp:
    ports:
      - "${WEB_PORT}:80"
    environment:
      - DEBUG=${DEBUG}
```

**3. The `env_file` attribute** (a separate file per service):

```yaml
services:
  webapp:
    image: example/webapp:latest
    env_file:
      - webapp.env
```

**4. Shell variables at runtime** (highest priority, good for temporary overrides):

```bash
DEBUG=true docker compose up
```

**Precedence (highest to lowest)**

1. `docker compose run -e` on the CLI
2. Shell environment variable
3. `environment` in the Compose file
4. `--env-file` on the CLI
5. `env_file` in the Compose file
6. The `.env` file
7. `ENV` set inside the Dockerfile

**Best practices**

- Never commit secrets. Use Docker Secrets or a secret manager for passwords and API keys.
- Keep `.env` files out of version control (add to `.gitignore`).
- Combine `env_file` for shared config with `environment` for per-service overrides.
- Use `docker compose config` to see the final merged configuration.
