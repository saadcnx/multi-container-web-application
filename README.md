# 🐳 Multi-Container Web App with Docker Compose

A production-ready web application stack built with Docker Compose, featuring a Python Flask API, PostgreSQL database, Redis cache, and Nginx reverse proxy.

---

## 📦 Tech Stack

| Service | Technology | Purpose |
|--------|-----------|---------|
| Web App | Python Flask + Gunicorn | REST API backend |
| Database | PostgreSQL 15 | Persistent data storage |
| Cache | Redis 7 | Session management & caching |
| Proxy | Nginx | Reverse proxy & load balancing |

---

## 🏗️ Architecture

```
                        ┌─────────────────────────────────────┐
                        │           app-network (bridge)       │
                        │                                       │
Client ──► Nginx:80 ──► │ ──► Flask/Gunicorn:5000              │
                        │         │           │                 │
                        │         ▼           ▼                 │
                        │   PostgreSQL:5432  Redis:6379         │
                        └─────────────────────────────────────┘
```

---

## 📁 Project Structure

```
.
├── docker-compose.yml          # Main orchestration file
├── docker-compose.prod.yml     # Production config with resource limits
├── init-db.sql                 # Database schema & seed data
├── redis.conf                  # Redis configuration
├── app/
│   ├── app.py                  # Flask application
│   ├── requirements.txt        # Python dependencies
│   └── Dockerfile              # App container image
└── nginx/
    ├── nginx.conf              # Main Nginx config
    └── default.conf            # Server block & proxy rules
```

---

## 🚀 Getting Started

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) `v20.10+`
- [Docker Compose](https://docs.docker.com/compose/install/) `v2.x`

### Clone & Run

```bash
git clone https://github.com/saadcnx/multi-container-web-application.git
cd multi-container-web-application
```

```bash
docker-compose up -d --build
```

That's it. All four services will start automatically in the correct order.

---

## 🔗 API Endpoints

Once running, the app is accessible at `http://localhost`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/` | Home — returns page view count & timestamp |
| `GET` | `/users` | Fetch all users |
| `POST` | `/users` | Create a new user |
| `GET` | `/health` | Health check for all services |

### Example Requests

```bash
# Home
curl http://localhost/

# Health check
curl http://localhost/health

# Create a user
curl -X POST http://localhost/users \
  -H "Content-Type: application/json" \
  -d '{"name": "John Doe", "email": "john@example.com"}'

# Get all users
curl http://localhost/users
```

---

## ⚙️ Configuration

Environment variables are defined in `docker-compose.yml`. Key variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `DB_HOST` | `db` | PostgreSQL hostname |
| `DB_NAME` | `webapp` | Database name |
| `DB_USER` | `postgres` | Database user |
| `DB_PASS` | `securepassword123` | Database password |
| `REDIS_HOST` | `redis` | Redis hostname |
| `REDIS_PORT` | `6379` | Redis port |

> ⚠️ Change default passwords before deploying to any public environment.

---

## 🛠️ Common Commands

```bash
# View running services
docker-compose ps

# Follow logs (all services)
docker-compose logs -f

# Follow logs (specific service)
docker-compose logs -f web

# Restart a service
docker-compose restart web

# Scale web service
docker-compose up -d --scale web=3

# Access PostgreSQL
docker-compose exec db psql -U postgres -d webapp

# Access Redis CLI
docker-compose exec redis redis-cli

# Stop everything
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

---

## 🏭 Production Deployment

Use the production compose file which includes resource limits and replicas:

```bash
docker-compose -f docker-compose.prod.yml up -d
```

Resource limits per service:

| Service | CPU Limit | Memory Limit |
|---------|-----------|--------------|
| Web (×3) | 0.5 cores | 512 MB |
| PostgreSQL | 1 core | 1 GB |
| Redis | 0.5 cores | 256 MB |
| Nginx | 0.25 cores | 128 MB |

---

## 💾 Data Persistence

All stateful data is stored in named Docker volumes:

- `postgres_data` — database files
- `redis_data` — Redis snapshots

Volumes survive container restarts and `docker-compose down`. Use `down -v` only when you want to wipe data.

### Backup

```bash
# Database backup
docker-compose exec db pg_dump -U postgres webapp > backup.sql

# Restore
docker-compose exec -T db psql -U postgres webapp < backup.sql
```

## SCREENSHOTS
<img width="1457" height="93" alt="image" src="https://github.com/user-attachments/assets/05d77958-8e47-41f2-bc4f-ac1de2d63681" />
<img width="1548" height="186" alt="image" src="https://github.com/user-attachments/assets/35c2f290-5505-42f8-8a7a-74dac6e8a17e" />
Horizontal scaling - 3 web instances running
<img width="1549" height="431" alt="image" src="https://github.com/user-attachments/assets/12278d6f-1ca3-4837-a74d-290f010db53d" />

---

## 🩺 Health Checks

All services have health checks configured. View status:

```bash
docker inspect --format='{{.State.Health.Status}}' webapp
docker inspect --format='{{.State.Health.Status}}' postgres_db
docker inspect --format='{{.State.Health.Status}}' redis_cache
```

---

## 🌐 Networking

All services communicate over a custom bridge network (`app-network`, subnet `172.20.0.0/16`). Services reference each other by name (e.g., `db`, `redis`, `web`) — no hardcoded IPs needed.

---

## 📄 License

MIT — feel free to use, modify, and distribute.
