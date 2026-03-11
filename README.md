
---

# 🛡️ Pocket ID (Docker Compose)

Pocket ID is a lightweight, self-hosted OIDC provider designed for simplicity and speed. This setup uses a pre-existing external network—perfect for those running a reverse proxy like Nginx Proxy Manager or Traefik.

## 🚀 Prerequisites

* **Docker & Docker Compose** installed.
* An existing Docker network named `newt_proxy`.
* A `.env` file in the same directory for configuration.

---

## 🛠️ Step-by-Step Installation

### 1. Create a Project Directory

```bash
mkdir pocket-id && cd pocket-id

```

### 2. Create the Configuration File

Pocket ID requires specific environment variables to function. Create a `.env` file:

```bash
nano .env

```

**Add these essential lines:**

```env
PUBLIC_APP_URL=https://auth.yourdomain.com
# Use a random string for the encryption key
BACKEND_SECRET=your_random_secret_here

```

### 3. Create the Docker Compose File

Create `docker-compose.yml` and paste the following configuration. This setup routes traffic through your `newt_proxy` network.

```yaml
services:
  pocket-id:
    image: ghcr.io/pocket-id/pocket-id:next
    restart: unless-stopped
    env_file: .env
    volumes:
      - ./data:/app/data
    healthcheck:
      test:
        - CMD
        - /app/pocket-id
        - healthcheck
      interval: 1m30s
      timeout: 5s
      retries: 2
      start_period: 10s
    networks:
      - newt_proxy
networks:
  newt_proxy:
    external: true

```

### 4. Launch the Service

```bash
docker compose up -d

```

### 5. Access & Finalize

1. Navigate to the URL defined in your `PUBLIC_APP_URL`.
2. **Create your Admin account** immediately upon first load.
3. Ensure your Reverse Proxy points to `pocket-id` on port **80**.

---

## ⚙️ Configuration Details

| Key | Value | Purpose |
| --- | --- | --- |
| **Image** | `pocket-id:next` | Uses the latest bleeding-edge features. |
| **Volumes** | `./data` | Persistent storage for users and settings. |
| **Network** | `newt_proxy` | Connects to your existing proxy infrastructure. |
| **Healthcheck** | Enabled | Automatically monitors container responsiveness. |

---

> **Pro Tip:** Since this uses an `external` network, ensure your proxy (Nginx/Traefik) is also joined to `newt_proxy` so they can communicate by container name.
