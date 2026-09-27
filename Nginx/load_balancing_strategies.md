# Load Balancing Strategies

Load balancing decides how incoming requests are spread across multiple servers. The goal is better performance, good use of resources, and high availability. Strategies fall into three groups: static, dynamic, and specialized.

## Table of Contents

1. [Static Strategies](#1-static-strategies)
2. [Dynamic Strategies](#2-dynamic-strategies)
3. [Specialized Strategies](#3-specialized-strategies)
4. [Summary Table](#4-summary-table)
5. [How to Choose](#5-how-to-choose)

---

## 1. Static Strategies

These do not look at the real-time load on servers.

- **Round Robin** — Requests go to each server in turn, one after another. Simple and even.
- **Weighted Round Robin** — Each server gets a fixed weight based on its capacity. Stronger servers get more requests.
- **IP Hash** — A hash of the client's IP decides the server, so the same client always hits the same server. Helps with session persistence.
- **Random** — Requests go to a random server, ignoring server state.

---

## 2. Dynamic Strategies

These look at the current state of servers and adapt.

- **Least Connections** — New requests go to the server with the fewest active connections. Good for uneven or long-running requests.
- **Weighted Least Connections** — Like Least Connections, but also considers each server's capacity.
- **Least Response Time** — Sends traffic to the fastest-responding server.
- **Resource-Based** — Routes based on real-time CPU or memory usage, usually via health checks or monitoring agents.
- **Custom Load** — Admins define custom rules (CPU, disk I/O, or app-specific metrics) to route traffic.

---

## 3. Specialized Strategies

- **Priority-Based** — Always sends traffic to the highest-priority server. Others stay as standby and are used only if the top ones fail.
- **URL Hash / Path-Based** — Routes based on the HTTP path or URL. This is application-layer (Layer 7) routing.
- **DNS-Based** — DNS responses rotate records or pick servers by location to spread load globally.
- **Global Server Load Balancing (GSLB)** — Sends users to the nearest or best-performing data center across regions for lower latency.

---

## 4. Summary Table

| Strategy | Category | Key Use / Criterion |
| :-- | :-- | :-- |
| Round Robin | Static | Simple, even distribution |
| Weighted Round Robin | Static | Distribute by server capacity |
| Random | Static | Random distribution |
| IP Hash | Static | Session persistence (stickiness) |
| Least Connections | Dynamic | Fewest active connections |
| Weighted Least Connections | Dynamic | Least loaded, considers weights |
| Least Response Time | Dynamic | Fastest responding server |
| Resource-Based | Dynamic | CPU / memory / disk usage |
| Priority-Based | Specialized | Failover, hot-spare servers |
| Custom | Specialized | Tailored to custom metrics |
| URL / Path-Based | Specialized | Layer 7, routes specific URLs |
| DNS / GSLB | Specialized | Multi-region, geo-aware |

---

## 5. How to Choose

The right strategy depends on:

- **Workload pattern** — even vs bursty traffic.
- **Server differences** — are all servers the same size, or mixed?
- **Session persistence** — does a user need to stay on the same server?
- **Performance goals** — do you need real-time adaptation to load?

For most simple setups, **Round Robin** or **Least Connections** works well. Use **IP Hash** when you need stickiness, and **GSLB** when you serve users across regions.

> For how these map to NGINX config (`weight`, `backup`, `upstream`, `split_clients`), see `NGINX Crash Course Brief.md`.
