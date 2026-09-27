# Docker Notes — Interview Prep

My personal Docker study notes, organized for quick revision. Written in simple, clear English.

## Contents

| # | File | What's Inside |
| :-- | :-- | :-- |
| 1 | [docker_01.md](./docker_01.md) | **Commands Reference** — CLI, networking, Dockerfile, and Compose commands. Plus image optimization, multi-container apps, and environment variables. |
| 2 | [docker_02.md](./docker_02.md) | **Docker Networking** — network drivers (bridge, host, overlay, etc.), when to use each, commands, and a working example. |
| 3 | [docker_03.md](./docker_03.md) | **Docker Compose Guide** — what it is, why and when to use it, and a full example Compose file. |

## Suggested Reading Order

1. Start with **docker_01.md** to get the core commands and concepts.
2. Read **docker_02.md** to understand how containers communicate.
3. Finish with **docker_03.md** to tie multiple containers together with Compose.

## Quick Interview Talking Points

- **Image vs Container:** An image is a read-only template. A container is a running instance of an image.
- **Dockerfile:** A script of instructions used to build an image.
- **CMD vs ENTRYPOINT:** `ENTRYPOINT` is the command that always runs; `CMD` gives default arguments that can be overridden.
- **Volumes:** Used to keep data outside the container so it survives restarts.
- **Bridge vs Host network:** Bridge isolates containers on a virtual network; host uses the machine's network directly for speed.
- **Multi-stage builds:** Keep build tools out of the final image to make it smaller and safer.
- **Docker Compose:** Defines and runs multi-container apps from one YAML file.
