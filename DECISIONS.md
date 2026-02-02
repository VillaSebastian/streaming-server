# Architectural and Design Decisions

This document records intentional decisions made during the development of the VPS Streaming Server.

The goal is to keep the system understandable, debuggable, and suitable for learning.

---

## Docker Compose layering

The project uses **multiple Docker Compose files**:

* `docker-compose.yml` — base services (HTTP Nginx)
* `docker-compose.prod.yml` — production-only services (Certbot, HTTPS)

They are combined explicitly using:

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml ...
```

This avoids conditional logic, profiles, or shell scripts.

---

## Explicit HTTPS bootstrapping

HTTPS is not enabled automatically on first run.

Instead, the process is:

1. Start HTTP-only Nginx
2. Run Certbot once to obtain certificates
3. Restart Nginx with HTTPS enabled

This mirrors how HTTPS works in reality and makes failures easy to diagnose.

---

## Certbot execution model

Certbot is run using:

```bash
docker compose run --rm certbot
```

Rationale:

* Certbot is not a long-running service
* Certificates are stored in shared volumes
* The container can be safely removed after execution

---

## Nginx template rendering

The official Nginx Docker image is used.

Key behavior:

* Templates must be placed in `/etc/nginx/templates/*.template`
* Templates are rendered using `envsubst` at container startup
* Rendered files appear in `/etc/nginx/conf.d/*.conf`

If templates are mounted to the wrong path, Nginx silently falls back to its default configuration.

---

## Environment variables

A single `.env` file is used for all environments.

Docker Compose:

* Reads `.env` automatically
* Injects variables into containers explicitly via `environment:`

Containers do not have access to host variables unless Docker passes them at runtime.

---

## Streaming roadmap

Planned streaming modes:

* **Quality mode** using LL-HLS
* **Low-latency mode** using WebRTC

The current infrastructure is intentionally minimal to support incremental learning and iteration.