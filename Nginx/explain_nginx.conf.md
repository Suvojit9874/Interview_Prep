# Understanding nginx.conf

A section-by-section explanation of the default `nginx.conf` file. This is the main configuration file that controls how NGINX runs.

## Table of Contents

1. [The Full Config File](#1-the-full-config-file)
2. [How the Blocks Fit Together](#2-how-the-blocks-fit-together)
3. [Global Settings](#3-global-settings)
4. [Events Block](#4-events-block)
5. [HTTP Block](#5-http-block)
   - [Basic Settings](#51-basic-settings)
   - [SSL Settings](#52-ssl-settings)
   - [Logging Settings](#53-logging-settings)
   - [Compression](#54-compression)
   - [Virtual Hosts](#55-virtual-hosts)
6. [Mail Block (Commented Out)](#6-mail-block-commented-out)
7. [Summary](#7-summary)

---

## 1. The Full Config File

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {
    ##
    # Basic Settings
    ##
    sendfile on;
    tcp_nopush on;
    types_hash_max_size 2048;
    # server_tokens off;
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL Settings
    ##
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # Logging Settings
    ##
    access_log /var/log/nginx/access.log;

    gzip on;
    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    ##
    # Virtual Host Configs
    ##
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}

#mail {
#   # See sample authentication script at:
#   # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#   # auth_http localhost/auth.php;
#   # pop3_capabilities "TOP" "USER";
#   # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#   server {
#       listen     localhost:110;
#       protocol   pop3;
#       proxy      on;
#   }
#
#   server {
#       listen     localhost:143;
#       protocol   imap;
#       proxy      on;
#   }
#}
```

---

## 2. How the Blocks Fit Together

NGINX config is organized into nested blocks (contexts):

```
        ---> {events}
[main] ---> {stream}
        ---> {http} ---> [server]   ---> {location}
                    ---> [upstream]
```

- **main** — the top level (global settings).
- **events** — connection handling settings.
- **http** — everything for the HTTP server. Inside it live `server` blocks (virtual hosts), which contain `location` blocks (URL routing), plus `upstream` blocks (backend groups for load balancing).
- **stream** — for raw TCP/UDP proxying (not shown in this file).

---

## 3. Global Settings

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;
```

- **`user www-data;`** — runs the worker processes as the `www-data` user, common for web apps.
- **`worker_processes auto;`** — sets the number of worker processes automatically based on CPU cores.
- **`pid /run/nginx.pid;`** — location of the file holding the main process ID.
- **`error_log ...;`** — where error logs are written.
- **`include .../modules-enabled/*.conf;`** — loads extra config for enabled modules.

---

## 4. Events Block

```nginx
events {
    worker_connections 768;
    # multi_accept on;
}
```

- **`worker_connections 768;`** — each worker can handle up to 768 connections at once.
- **`multi_accept on;`** (commented out) — if on, a worker accepts all new connections at once instead of one at a time.

---

## 5. HTTP Block

Wraps all HTTP-related settings.

### 5.1 Basic Settings

```nginx
sendfile on;
tcp_nopush on;
types_hash_max_size 2048;
include /etc/nginx/mime.types;
default_type application/octet-stream;
```

- **`sendfile on;`** — efficient file transfer; the kernel sends files straight from disk to the network.
- **`tcp_nopush on;`** — sends headers in one packet for better efficiency.
- **`types_hash_max_size 2048;`** — max size of the MIME types hash table.
- **`server_tokens off;`** (commented out) — would hide the NGINX version number from responses.
- **`include .../mime.types;`** — maps file extensions to MIME types.
- **`default_type application/octet-stream;`** — default content type when none matches.

### 5.2 SSL Settings

```nginx
ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
ssl_prefer_server_ciphers on;
```

- **`ssl_protocols ...`** — which TLS versions are allowed. Old, insecure SSLv3 is dropped (POODLE attack).
- **`ssl_prefer_server_ciphers on;`** — the server chooses the cipher, not the client, for better security.

### 5.3 Logging Settings

```nginx
access_log /var/log/nginx/access.log;
```

- **`access_log ...;`** — file where every incoming request is logged.

### 5.4 Compression

```nginx
gzip on;
# gzip_vary on;
# gzip_proxied any;
# gzip_comp_level 6;
# gzip_buffers 16 8k;
# gzip_http_version 1.1;
# gzip_types text/plain text/css application/json application/javascript ...;
```

- **`gzip on;`** — compresses responses to save bandwidth.
- The commented options fine-tune compression:
  - **`gzip_vary on;`** — adds the `Vary: Accept-Encoding` header.
  - **`gzip_proxied any;`** — compresses proxied requests too.
  - **`gzip_comp_level 6;`** — compression level (1 = fastest, 9 = smallest).
  - **`gzip_buffers 16 8k;`** — buffers used for compression.
  - **`gzip_http_version 1.1;`** — enables gzip only for HTTP/1.1.
  - **`gzip_types ...`** — which content types to compress.

### 5.5 Virtual Hosts

```nginx
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

- Loads extra config files for individual sites and virtual hosts. This is where your per-site `server` blocks usually live.

---

## 6. Mail Block (Commented Out)

```nginx
#mail {
#   server {
#       listen     localhost:110;
#       protocol   pop3;
#       proxy      on;
#   }
#   server {
#       listen     localhost:143;
#       protocol   imap;
#       proxy      on;
#   }
#}
```

This block is disabled. If enabled (and NGINX is built with mail support), it would let NGINX proxy mail protocols like POP3 and IMAP.

---

## 7. Summary

- This config sets up NGINX to run as an efficient and secure HTTP server.
- It covers security (SSL), performance (worker processes, sendfile, compression), and extensibility (includes, virtual hosts).
- Many options are commented out and left for more specific tuning later (like the gzip details and the mail block).
