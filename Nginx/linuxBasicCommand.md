# Linux, NGINX & Docker Commands Cheat Sheet

A quick reference for the Linux, NGINX, and Docker host commands you use most. Handy for interview revision and day-to-day work.

## Table of Contents

1. [Basic Linux Commands](#1-basic-linux-commands)
2. [System and Process Info](#2-system-and-process-info)
3. [Software Installation (Debian/Ubuntu)](#3-software-installation-debianubuntu)
4. [Managing Services (systemctl)](#4-managing-services-systemctl)
5. [NGINX: Install to Manage](#5-nginx-install-to-manage)
6. [Docker on a Linux Host](#6-docker-on-a-linux-host)
7. [Vim Basics](#7-vim-basics)
8. [Quick Cheat Sheet Tables](#8-quick-cheat-sheet-tables)

---

## 1. Basic Linux Commands

File and folder navigation.

| Task | Command |
| :-- | :-- |
| List files | `ls` |
| List detailed info (with hidden) | `ls -la` |
| Change directory | `cd /path/to/directory` |
| Go up one directory | `cd ..` |
| Print current directory | `pwd` |
| Copy a file | `cp source.txt destination.txt` |
| Move or rename a file | `mv old.txt new.txt` |
| Remove a file | `rm file.txt` |
| Remove a directory | `rm -r directory` |
| Create a file | `touch filename` |
| Create a directory | `mkdir dirname` |

---

## 2. System and Process Info

| Task | Command |
| :-- | :-- |
| Show running processes | `ps aux` |
| Monitor live processes | `top` |
| Kill a process by PID | `kill PID` (or `kill -9 PID` to force) |
| Show disk usage | `df -h` |
| Show memory usage | `free -h` |
| Show current user | `whoami` |
| Show system info | `uname -a` |
| View live logs | `tail -f /var/log/syslog` |
| Shutdown | `sudo shutdown now` |
| Reboot | `sudo reboot` |

---

## 3. Software Installation (Debian/Ubuntu)

| Task | Command |
| :-- | :-- |
| Update package lists | `sudo apt update` |
| Upgrade installed packages | `sudo apt upgrade` |
| Install a package | `sudo apt install <package>` |
| Remove a package | `sudo apt remove <package>` |

---

## 4. Managing Services (systemctl)

| Task | Command |
| :-- | :-- |
| Start a service | `sudo systemctl start <service>` |
| Restart a service | `sudo systemctl restart <service>` |
| Stop a service | `sudo systemctl stop <service>` |
| Enable on boot | `sudo systemctl enable <service>` |
| Disable on boot | `sudo systemctl disable <service>` |
| Check status | `sudo systemctl status <service>` |

---

## 5. NGINX: Install to Manage

**Install (Debian/Ubuntu)**

```bash
sudo apt update
sudo apt install nginx
```

**Start, enable, check**

```bash
sudo systemctl start nginx      # start
sudo systemctl enable nginx     # start on boot
sudo systemctl stop nginx       # stop
sudo systemctl restart nginx    # restart
sudo systemctl status nginx     # check status
```

**Key config locations**

- Main config: `/etc/nginx/nginx.conf`
- Site configs: `/etc/nginx/sites-available/` and `/etc/nginx/sites-enabled/`

**Test and reload**

```bash
sudo nginx -t                   # test config for errors
sudo systemctl reload nginx     # reload after a change
sudo nginx -s reload            # reload (alternative)
```

> Always run `sudo nginx -t` before reloading to catch config mistakes.

---

## 6. Docker on a Linux Host

**Install (Debian/Ubuntu)**

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg
curl -fsSL https://get.docker.com | sudo bash
```

**Service commands**

```bash
sudo systemctl status docker    # check status
sudo systemctl start docker     # start
sudo systemctl enable docker    # start on boot
```

**Basic container commands**

```bash
sudo docker ps                              # running containers
sudo docker ps -a                           # all containers
sudo docker pull nginx                      # pull an image
sudo docker run --name mynginx -d -p 8080:80 nginx   # run a container
sudo docker stop mynginx                    # stop
sudo docker rm mynginx                      # remove
sudo docker system df                       # disk usage by Docker
sudo docker system prune                    # remove unused images/containers
```

> For the full Docker command list, see `../Docker/docker_01.md`.

---

## 7. Vim Basics

| Task | Command |
| :-- | :-- |
| Enter insert mode | `i` |
| Save and exit | `Esc` then `:wq` |
| Exit without saving | `Esc` then `:q!` |
| Delete a line | `dd` |
| Copy a line | `yy` (yank) |
| Paste | `p` |

---

## 8. Quick Cheat Sheet Tables

**Linux essentials**

| Task | Command |
| :-- | :-- |
| Update package list | `sudo apt update` |
| Install a package | `sudo apt install <package>` |
| List files | `ls -la` |
| Change directory | `cd <path>` |
| Check disk space | `df -h` |
| Show processes | `ps aux` |
| Kill a process | `kill -9 <PID>` |

**NGINX essentials**

| Task | Command |
| :-- | :-- |
| Start / stop / restart | `sudo systemctl start\|stop\|restart nginx` |
| Reload config | `sudo systemctl reload nginx` |
| Test config | `sudo nginx -t` |
| Main config file | `/etc/nginx/nginx.conf` |

**Docker essentials**

| Task | Command |
| :-- | :-- |
| List all containers | `docker ps -a` |
| Run with a shell | `docker run -it ubuntu /bin/bash` |
| Start / stop | `docker start\|stop <id>` |
| Enter a container | `docker exec -it <id> bash` |
| Copy a file in | `docker cp file.txt <id>:/path` |
| Build an image | `docker build -t my-image .` |
| Remove container / image | `docker rm <id>` / `docker rmi <image>` |
