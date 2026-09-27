# NGINX Notes — Interview Prep

My personal NGINX and Linux study notes, organized for quick revision. Written in simple, clear English.

## Contents

| # | File | What's Inside |
| :-- | :-- | :-- |
| 1 | [NGINX Crash Course Brief.md](./NGINX%20Crash%20Course%20Brief.md) | **Core concepts** — static hosting, reverse proxy, load balancing, SSL, plus load-balancer keywords and canary / blue-green deployments. |
| 2 | [explain_nginx.conf.md](./explain_nginx.conf.md) | **nginx.conf explained** — a line-by-line walkthrough of the main config file and how its blocks fit together. |
| 3 | [load_balancing_strategies.md](./load_balancing_strategies.md) | **Load balancing strategies** — static, dynamic, and specialized algorithms, with a summary table and how to choose. |
| 4 | [linuxBasicCommand.md](./linuxBasicCommand.md) | **Commands cheat sheet** — Linux, NGINX, and Docker host commands, plus Vim basics. |

## Suggested Reading Order

1. **linuxBasicCommand.md** — get comfortable with the shell and service commands.
2. **NGINX Crash Course Brief.md** — the main concepts you'll be asked about.
3. **explain_nginx.conf.md** — understand the config file structure.
4. **load_balancing_strategies.md** — go deeper on load balancing theory.

## Quick Interview Talking Points

- **What is NGINX?** A high-performance web server, also used as a reverse proxy, load balancer, and for caching.
- **Reverse proxy vs load balancer:** A reverse proxy forwards requests to a backend and hides it; a load balancer spreads requests across multiple backends.
- **Config structure:** `main` → `events`, `http` → `server` (virtual host) → `location`, plus `upstream` for backend groups.
- **Default load balancing:** Round-robin. You can change it with `least_conn`, `ip_hash`, or `weight`.
- **Reload safely:** Run `sudo nginx -t` to test, then `sudo systemctl reload nginx`.
- **SSL:** Terminate HTTPS at NGINX with `listen 443 ssl;` and certificate paths; redirect HTTP to HTTPS with a `301`.
- **Deployments:** Use `split_clients` for canary rollouts and upstream switching for blue-green.
