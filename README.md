# VPS Streaming Server

This project is a self-hosted videogame live streaming server, designed to be run on a VPS using Docker and Nginx.

The goal of the project is to learn streaming internals (HLS, LL-HLS, WebRTC) by building and operating the full stack, starting from a minimal, understandable infrastructure.

---

## Requirements

* Docker
* Docker Compose
* A VPS (for production)
* A domain name pointing to the VPS (production only)

---

## Environment variables

The project relies on a single `.env` file.

The `.env` file **must** define:

```env
DOMAIN=domain.com
EMAIL=user@domain.com
```

* `DOMAIN` is used by Nginx and Certbot
* `EMAIL` is used by Certbot for Let’s Encrypt registration

---

## Folder structure (simplified)

```text
.
├── docker-compose.yml
├── docker-compose.prod.yml
├── .env
├── nginx/
│   ├── templates/
│   │   ├── http.conf.template
│   │   └── https.conf.template
│   └── html/
│       └── index.html
├── certbot/
│   └── www/
└── DECISIONS.md
```

---

## Running the project

### Local development

Local development runs **HTTP only** and does not require a domain.

```bash
docker compose up -d
```

The site will be available at:

```
http://localhost
```

---

### Production (VPS)

Before running in production:

* The domain **must** point to the VPS IP
* Ports **80 and 443** must be open
* The `.env` file must contain a valid domain and email

#### 1. Start Nginx (HTTP only)

```bash
docker compose up -d
```
---

#### 2. Obtain HTTPS certificates (Certbot)

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml run --rm certbot
```

This:

* Starts a temporary Certbot container
* Writes certificates to shared volumes
* Stops and removes the container automatically

---

#### 3. Start Nginx with HTTPS enabled

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d nginx
```

At this point:

* HTTPS is active
* HTTP redirects to HTTPS
* Nginx runs autonomously until certificate renewal is needed

---

## Notes

* The official Nginx Docker image automatically processes templates placed in `/etc/nginx/templates/*.template`
* Environment variables are injected using `envsubst` at container startup
* If templates are not mounted to the correct path, Nginx silently falls back to its default configuration

---

This setup intentionally favors clarity and debuggability over automation magic.
Future steps will extend this base with streaming protocols and media pipelines.