# Docker Networking

Docker networking lets containers talk to each other, to the host, and to the outside world. Knowing this well helps you build secure and scalable container setups.

## Table of Contents

1. [Why Docker Networking Is Needed](#1-why-docker-networking-is-needed)
2. [Network Drivers (Types)](#2-network-drivers-types)
3. [When to Use Which Network](#3-when-to-use-which-network)
4. [Common Networking Commands](#4-common-networking-commands)
5. [Quick Example](#5-quick-example)
6. [Best Practices](#6-best-practices)

---

## 1. Why Docker Networking Is Needed

- **Container isolation:** By default containers are isolated. Networking allows controlled communication.
- **Service discovery:** Containers can find each other by name inside a user-defined network.
- **Security:** Networks control which containers can reach each other.
- **Scalability:** Needed to scale services across many containers.
- **External access:** Containers often need to reach the host, the internet, or cloud services.

---

## 2. Network Drivers (Types)

Docker ships with several built-in network drivers, each for a different use case.

| Driver | Purpose | When to Use |
| :-- | :-- | :-- |
| **bridge** | Default for standalone containers on one host | Most common; simple communication on a single host |
| **host** | Removes isolation and uses the host's network directly | Highest performance for trusted containers; avoid on shared hosts |
| **none** | Disables all networking | Fully isolated workloads or debugging |
| **overlay** | Connects containers across multiple hosts | Docker Swarm or Kubernetes; microservices across nodes |
| **macvlan** | Gives a container its own MAC address like a real device | Integrating with legacy apps or network appliances |
| **ipvlan** | Like macvlan but routes at the IP layer | Advanced, less common networking needs |
| **custom** | A user-defined bridge network | Custom topologies with DNS-based service discovery |

---

## 3. When to Use Which Network

- **bridge:** Most local development and single-host setups. Gives DNS lookup by container name.
- **host:** High-performance containers that need to skip Docker's virtual network (e.g. a proxy or monitoring tool).
- **none:** When a container must have no networking at all (e.g. security testing).
- **overlay:** When using Swarm or an orchestrator so containers on different hosts can communicate.
- **macvlan:** When a container must appear as a real device on the LAN.
- **custom:** When you want DNS-based discovery and clean isolation between app tiers.

---

## 4. Common Networking Commands

**Create and manage networks**

- `docker network ls` — list all networks
- `docker network create <name>` — create a bridge network
- `docker network create --driver overlay <name>` — create an overlay network (Swarm)
- `docker network rm <name>` — remove a network

**Inspect a network**

- `docker network inspect <name>` — show full configuration

**Connect and disconnect containers**

- `docker network connect <network> <container>` — attach a container
- `docker network disconnect <network> <container>` — detach a container

**Run a container on a network**

- `docker run --network <network> <image>` — start attached to a network

---

## 5. Quick Example

Create a network, run two containers on it, and confirm they can reach each other by name:

```bash
# Create a user-defined bridge network
docker network create my-bridge

# Run two containers on that network
docker run --network my-bridge --name container1 -d nginx
docker run --network my-bridge --name container2 -d alpine sleep 3600

# Ping container1 from container2 (works because of DNS by name)
docker exec -it container2 ping container1
```

Because both containers share a user-defined network, `container2` can reach `container1` using its name instead of an IP address.

---

## 6. Best Practices

- **Use user-defined bridge networks for most apps.** They give automatic DNS service discovery.
- **Use separate networks for sensitive workloads.** Better isolation and security.
- **Use overlay networks for multi-node setups.** Needed to scale microservices across hosts.
- **Limit `host` mode.** Only use it when you trust the container and need the performance.
