## 🧩 Multi-Container Architecture Overview

In real-world applications, it's rare to have everything bundled into a single container. Instead, you typically split your system into several **independent yet connected containers**—each representing a service or component (e.g., web frontend, API backend, database, message queue, cache).

This approach promotes:

- **Separation of concerns**: Each container runs a single service.
- **Scalability**: You can scale specific services independently.
- **Resilience**: Isolated failures don’t crash the whole stack.
- **Reusability**: Common services like databases or reverse proxies can be reused across projects.

---

### 🧪 Example Architecture

```
+-------------+      +--------------+      +---------------+
|   Frontend  | <--> |  API Server  | <--> |  PostgreSQL DB|
+-------------+      +--------------+      +---------------+
       ↑                                         ↑
       |                                         |
+-------------+                           +---------------+
|   NGINX      |                           | Redis Cache   |
+-------------+                           +---------------+
```

Each block above is a separate container, communicating via internal Docker networks.

---

### 🧱 Typical Multi-Container Stack

| Service         | Role                             |
| --------------- | -------------------------------- |
| **Frontend**    | UI layer (React/Vue/Angular)     |
| **Backend/API** | Server logic (Node.js/Django)    |
| **Database**    | Data storage (PostgreSQL, MySQL) |
| **Cache**       | Performance boost (Redis)        |
| **Proxy**       | Routing and SSL (NGINX, Traefik) |

> 🧠 These services work independently and communicate over Docker’s internal bridge or user-defined networks.

---

### 🧪 Benefits of Multi-Container Architecture over Monolithic Architecture

- Faster builds and isolated testing
- Easier CI/CD deployment pipelines
- Clear ownership and team boundaries
- Language and technology flexibility

---

This architecture is where Docker Compose truly shines—so next, we’ll explore **Introduction to Docker Compose**: what it is, how it simplifies orchestration, and how it fits into your workflow.

<br>

## 🛠️ Introduction to Docker Compose

**Docker Compose** is a powerful tool that lets you define and run multi-container applications with a simple YAML configuration file. Instead of manually running `docker run` commands for each service, Compose automates it all—networks, volumes, environment variables, dependencies, and more.

---

### 🔧 What Docker Compose Does

- Defines a **stack of services** using a `docker-compose.yml` or `compose.yml` file (extension .yaml is also supported)
- Automatically sets up **isolated networks** and **named volumes**
- Starts, stops, and manages **multiple containers** with a single command
- Simplifies development by mirroring real production setups

---

### 📦 Why Use Compose?

| Feature               | Benefit                                |
| --------------------- | -------------------------------------- |
| Declarative config    | One file defines everything            |
| Automated networking  | Containers discover each other by name |
| Volume management     | Persistent and shared data is easy     |
| Service orchestration | One command spins up your whole stack  |
| Environment support   | Use `.env` files to control config     |

> 🧠 Compose is great for local development, staging environments, and automated testing pipelines. For production-grade orchestration, tools like Kubernetes take over.

---

### 🧪 Basic Compose Workflow

1. **Define services in `docker-compose.yml`**
2. Start your stack:
   ```bash
   docker compose up
   ```
3. Stop everything:
   ```bash
   docker compose down
   ```
4. Monitor:
   ```bash
   docker compose logs -f
   docker compose ps
   ```

---

### 🧠 Best Practices

- ✅ Organize Compose files in the root of your project
- ✅ Use **named volumes** for important data (like databases)
- ✅ Use `.env` files to parameterize settings
- ✅ Use `depends_on` for service start ordering
- ✅ Separate `build:` and `image:` depending on the scenario

---

Up next: we’ll dive into **Writing a `docker-compose.yml` File**—step-by-step, including ports, volumes, environments, build context, and service definitions.

<br>

## 🧾 Writing a `docker-compose.yml` or `compose.yml` File

The `docker-compose.yml` (legacy) or `compose.yml` (new) file defines your entire multi-container setup in one declarative configuration. It's clean, portable, and easy to version-control—like infrastructure as code for containers.

---

### 🧪 Minimal Example

```yaml
version: "3.9" # Version of Compose file format is important for compatibility, but version option is optional now - 3.9 is latest stable.

services:
  web:
    image: nginx
    ports:
      - "8080:80"

  app:
    build: ./app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

### 🧩 Service Configuration Options

| Key           | Description                        | Example                                  |
| ------------- | ---------------------------------- | ---------------------------------------- |
| `image`       | Use an existing image              | `nginx`, `postgres:15`                   |
| `build`       | Build from Dockerfile              | `build: ./app`                           |
| `ports`       | Map host ports                     | `"8080:80"`                              |
| `environment` | Set env vars                       | `NODE_ENV=production`                    |
| `volumes`     | Mount volumes (named or host path) | `pgdata:/var/lib/...`                    |
| `depends_on`  | Control start order                | `depends_on: [db]`                       |
| `command`     | Override CMD from image            | `npm start`                              |
| `restart`     | Restart policy                     | `always`, `on-failure`, `unless-stopped` |

---

### 📦 Volume Declaration

Declare named volumes separately under `volumes:` at the bottom of the file:

```yaml
volumes:
  pgdata:
```

This enables persistence and reuse between containers and builds.

---

### 📂 Build Context and Dockerfile

Use `build:` to specify where the Dockerfile is located:

```yaml
build:
  context: ./app
  dockerfile: Dockerfile.dev
```

You can also pass build arguments:

```yaml
args:
  NODE_VERSION: 18
```

---

### 🧠 Best Practices

- ✅ Use version `3.9` for maximum compatibility
- ✅ Use `build:` for your own services; `image:` for third-party components
- ✅ Keep one service per concern (web, api, db, cache)
- ✅ Split secrets into `.env` file instead of hardcoding
- ✅ Use named volumes for data you want to persist

---

Next up: we’ll explore **Running and Managing Multi-Container Apps**—how to bring your stack up, shut it down, and monitor service health.

<br>

## 🚀 Running and Managing Multi-Container Apps

With your `docker-compose.yml` file defined, you're now ready to launch, monitor, and manage your entire stack using Compose commands. These workflows are designed to be simple and repeatable—ideal for local development and CI environments.

---

### 🧪 Starting Your Stack

Start all defined services:

```bash
docker compose up
```

Or run in **detached mode** (background):

```bash
docker compose up -d
```

This creates containers, networks, and volumes as defined in your Compose file.

> Note that command **docker compose build** is not needed anymore, as **docker compose up** will automatically build images if they are not present.

---

### 🛑 Stopping and Removing Services

Gracefully stop running services (containers and networks):

```bash
docker compose down
```

Remove volumes and networks too:

```bash
docker compose down -v --remove-orphans
```

> 🧠 This cleans up everything, making it useful for clean rebuilds or switching branches.

Note that `docker compose down` will stop and remove all containers defined in the Compose file, but it will not remove any images that were built or pulled. If you want to remove those as well, you can use `docker compose down --rmi all`.

---

### 🧭 Viewing Service Status

Check which services are running:

```bash
docker compose ps
```

View container details:

```bash
docker inspect <service-name>
```

---

### 📺 Monitoring Logs

Stream all container logs:

```bash
docker compose logs -f
```

Or isolate to a specific service:

```bash
docker compose logs -f app
```

> 🧠 Use `--tail` and `--since` for focused debugging.

---

### 🔄 Restarting Services

Restart all containers:

```bash
docker compose restart
```

Restart just one:

```bash
docker compose restart web
```

Set automatic restart policy in your Compose file:

```yaml
restart: unless-stopped
```

---

### 🧪 Running Commands Inside Services

```bash
docker compose exec app bash
```

Use for interactive debugging or script execution.

---

### 🧼 Best Practices

- ✅ Use `up -d` for long-running services.
- ✅ Use `down -v` to rebuild with a clean slate.
- ✅ Monitor logs with `logs -f` while troubleshooting.
- ✅ Use `exec` instead of `attach` for safe command execution.
- ✅ Automate `compose up/down` in CI with health checks and wait scripts.

---

Next up: we’ll explore **Service Dependencies and Wait-for Strategies**—how to ensure services start in the correct order and communicate reliably.

<br>

## 🔄 Service Dependencies and Wait-for Strategies

In multi-container setups, services often rely on others being **available and ready**—like an app needing its database or cache. Docker Compose provides basic dependency mechanisms, but reliable startup coordination often requires more.

---

### 🧬 Basic Dependencies with `depends_on`

```yaml
services:
  app:
    depends_on:
      - db

  db:
    image: postgres
```

This ensures `db` **starts before** `app`—but does **not wait for `db` to be ready** (i.e. accepting connections). It’s a basic sequencing tool.

> 🧠 `depends_on` controls container start **order**, not **readiness**. A DB may still be initializing when `app` starts.

---

### ⏳ Wait-for Scripts (Reliable Coordination)

To ensure a service **waits until another is ready**, use "wait-for-it" scripts or tools like `wait-for`, `dockerize`, or `bash loops`.

#### Example (entrypoint.sh - this is a script that waits for the database to be ready before starting the app):

```bash
#!/bin/sh

until nc -z db 5432; do
  echo "Waiting for database..."
  sleep 2
done

exec node app.js
```

Dockerfile:

```Dockerfile
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

Compose:

```yaml
services:
  app:
    build: .
    depends_on:
      - db
    entrypoint: /entrypoint.sh

  db:
    image: postgres
```

This ensures your app container only launches once `db` is reachable on port `5432`.

---

### 🧪 Using Healthchecks (Advanced)

You can mark a container as "healthy" using Docker’s health check mechanism.

Example:

```yaml
services:
  db:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      retries: 5

  app:
    depends_on:
      db:
        condition: service_healthy
```

> ✅ Compose v3 (used with Docker Swarm) supports `condition: service_healthy`. The regular Compose CLI does **not** enforce this in all versions.

---

### 🧼 Best Practices

- ✅ Always use wait-for scripts when startup race conditions matter.
- ✅ Use health checks for robust readiness detection.
- ✅ Avoid hardcoding long sleep delays—they’re unreliable.
- ✅ Document dependencies clearly in your project README.

---

Next up: we’ll explore **Networking Between Containers**—how Compose creates virtual networks, how services communicate by name, and how to design secure traffic flows.

<br>

## 🌐 Container Networking in Docker Compose

By default, Docker Compose sets up an **isolated network** for your services to talk to each other securely using service names as hostnames.

---

### 🛠️ Automatic Networking Example

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"

  db:
    image: postgres
```

🔹 `app` can connect to `db` using `db:5432`—no need for IP addresses or extra setup.
🔹 Services within the same Compose file share a default bridge network.

---

### 🏷️ Custom Networks

You can define custom networks to isolate traffic or connect containers across projects.

```yaml
networks:
  backend:
  frontend:

services:
  app:
    networks:
      - frontend
      - backend

  db:
    networks:
      - backend
```

> 🚦Custom networks allow for better separation of concerns and fine-grained control.

---

### 🔒 Security and Isolation Tips

- 🛡️ Keep sensitive services (DB, Redis) on private/internal networks.
- 🚫 Never expose internal services with `ports` unless necessary.
- ✅ Use environment variables to pass credentials—not hardcoded values.

---

### 🧑‍🔬 Debugging Networking Issues

- 🧪 Use `docker network ls` to see existing networks.
- 📡 `docker inspect <network>` reveals connected containers.
- 🕵️ Inside a container, use tools like `ping`, `curl`, `netcat` for testing.

---

### 🧩 Best Practices

- 🎯 Prefer service names over IPs—they’re more portable.
- 📦 Group services with similar access patterns into separate networks.
- 📜 Document networking assumptions and naming conventions.

---

Coming up next: **Volumes and Persistent Data**—so your app’s progress doesn’t vanish into the void every time the container restarts. 💾✨

<br>

## 💾 Using Volumes Between Services

In multi-container setups, volumes are essential for sharing and persisting data across services. Whether you're storing database files, caching data, or sharing static assets, volumes help containers work together while keeping data safe across rebuilds and restarts.

---

### 🧬 Named Volumes in Compose

Named volumes are declared once and can be reused across multiple services.

```yaml
volumes:
  shared-data:

services:
  api:
    image: my-api
    volumes:
      - shared-data:/app/uploads

  processor:
    image: image-processor
    volumes:
      - shared-data:/data/uploads
```

This configuration allows both `api` and `processor` to share access to the same volume. It’s ideal for workflows like uploading a file in `api` and processing it in `processor`.

---

### 📦 Using Bind Mounts Instead

For dev environments where you want to share code or files on the host:

```yaml
volumes:
  - ./shared:/app/shared
```

Bind mounts link a host directory to your containers. Unlike named volumes, they don't persist independently of the host file system.

---

### 🛡️ Access Permissions

Ensure containers have read/write access as needed:

```yaml
volumes:
  - shared-data:/app/uploads:ro # read-only
  - shared-data:/data/uploads:rw # read/write
```

> 🧠 Use `:ro` for services that only need to consume data, not modify it.

---

### 🧪 Typical Use Cases

| Volume        | Used By         | Purpose                               |
| ------------- | --------------- | ------------------------------------- |
| `pgdata`      | `postgres`      | Persist database across restarts      |
| `shared-data` | `api`, `worker` | Transfer media, logs, or job payloads |
| `/app/static` | `web`, `proxy`  | Serve static frontend files           |

---

### 📂 Inspect Volumes

List volumes:

```bash
docker volume ls
```

Inspect a volume:

```bash
docker volume inspect shared-data
```

Clean up unused volumes:

```bash
docker volume prune
```

---

### 🧼 Best Practices

- ✅ Use named volumes for services that need persistence or shared access
- ✅ Prefer bind mounts for live development, not production
- ✅ Mount volumes under predictable paths for clarity (`/data`, `/app`, etc.)
- ✅ Use `:ro` to protect volumes from accidental changes
- ❌ Avoid mixing bind mounts and named volumes unless necessary

---

Next up: we’ll explore **Environment Configuration**—how to use `.env` files with Compose and parameterize your stack for dev, staging, and production.

<br>

## 🧪 Environment Configuration with Compose

Docker Compose supports flexible environment configuration using `.env` files, inline variables, and multiple Compose files. This allows you to adapt your setup to different environments like development, staging, or production—without changing your core `docker-compose.yml`.

---

### 📁 `.env` File Basics

Place a `.env` file in the same directory as your Compose file:

```
NODE_ENV=production
POSTGRES_DB=mydb
POSTGRES_PASSWORD=secret
```

Use these variables in your Compose file:

```yaml
services:
  app:
    image: node:18
    environment:
      - NODE_ENV=${NODE_ENV}

  db:
    image: postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

---

### 🧠 Best Practices for `.env` Files

- ✅ Keep `.env` files out of version control (`.gitignore`)
- ✅ Document required variables in README
- ✅ Use defaults with fallback syntax:

  ```yaml
  environment:
    - DEBUG=${DEBUG:-false}
  ```

- ❌ Don’t store secrets in plaintext for production—use secret managers instead.

---

### 🔄 Switching Environments

Use different `.env` files for each stage:

- `.env.dev`
- `.env.staging`
- `.env.prod`

You can override the default `.env` file by setting the `COMPOSE_ENV_FILE` environment variable or using shell tricks:

```bash
cp .env.prod .env && docker compose up
```

---

### 🧪 Inline Environment Variables

You can also set variables on the fly:

```bash
NODE_ENV=development docker compose up
```

Or use shell exports:

```bash
export NODE_ENV=development
docker compose up
```

---

### 🧩 Multiple Compose Files

Split configuration by stage:

```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up
```

> Later files override earlier ones—so you can swap out services, change images, tweak configs per environment.

---

### 🧼 Organizational Tips

| Environment File              | Purpose                         |
| ----------------------------- | ------------------------------- |
| `.env`                        | Default values (dev or base)    |
| `.env.staging`                | Staging-specific configs        |
| `.env.prod`                   | Secrets, production-only values |
| `docker-compose.override.yml` | Dev tweaks (e.g. hot reload)    |

---

Next up: we’ll explore **Scaling Services**—how to run multiple instances of a container, balance load, and prepare for high availability.

<br>

## 🚀 Scaling Services with Docker Compose

Scaling lets you run multiple instances of a service for redundancy, performance, or load balancing.

---

### 🧱 How to Scale a Service

Use the `--scale` flag:

```bash
docker compose up --scale web=3
```

This spins up three containers for the `web` service, assuming it’s stateless and supports parallel replicas.

In Compose YAML:

```yaml
services:
  web:
    image: nginx
    deploy:
      replicas: 3
```

> Note: `deploy` is ignored unless using **Docker Swarm**. Use the CLI scale flag in local Compose setups.

---

### ⚙️ Load Balancing

Use a reverse proxy like **Traefik** or **NGINX** to route traffic across your replicas. They can monitor container health and distribute requests intelligently.

---

### 🎯 Stateless Services Work Best

✅ Good candidates:

- Web frontends
- API gateways
- Workers consuming queues

❌ Hard to scale:

- Services with local state or persistent storage
- Databases (use clustering or managed services instead)

---

### 🔐 Bonus Tip: Health Checks

Ensure stability with health checks:

```yaml
services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
```

Healthy containers stay in rotation—unhealthy ones get restarted automatically.

<br>

## 📘 **From Mosh's Docker Course**:

- Note that our app is named `vidly` (root directory) and consists of a frontend, backend, and database (frontend and backend are subdirectories). The frontend is a React app, the backend is a Node.js API, and the database is MongoDB.

```yaml
version: "3.9"

services:
  web:
    depends_on:
      - api
    build: ./frontend
    ports:
      - "3000:3000"

  web-test:
    image: vidly-web
    volumes:
      - ./frontend:/app
    command: npm test

  api:
    depends_on:
      - db
    build: ./backend
    ports:
      - "3001:3001"
    environment:
      DB_URL: mongodb://db/vidly
    volumes:
      - ./backend:/app
    command: ./docker-entrypoint.sh

  db:
    image: mongo:4.0-xenial
    ports:
      - "27017:27017"
    volumes:
      - vidly:/data/db # MongoDB by default uses /data/db for data storage

volumes:
  vidly:
```

This compose file is defined in the root directory and consists of `frontend` and `backend` sub-directories. So `web` (frontend) service `build` property is set to `./frontend` because a `Dockerfile` for building frontend image is defined there. Same logic applies for `api` (backend).
For a `db` (database) we are not building our own image but pulling one from registry (Docker Hub).

We are also mapping ports so that we can access our services from the host machine. For example, `web` service is accessible on port `3000` of the host machine, while `api` service is accessible on port `3001`, and `db` service is accessible on port `27017` (MongoDB default port). This will create a bridge between the host and the containers, allowing us to interact with them easily. `vidly` will be the name of the database created in the `db` service.

Environment variables are used to configure the `api` service, specifically the database connection URL. Basically, it is used by the backend to connect to the MongoDB database running in the `db` service (which is a MongoDB container).

Volumes (named) are used to persist data in the `db` service. The `vidly` volume is created and mounted to `/data/db` (MongoDB by default uses /data/db for data storage - read documentation) inside the MongoDB container, ensuring that data is not lost when the container is stopped or removed.
Since we're using a named volume, Docker will create it automatically if it doesn't exist. This allows us to persist data across container restarts and upgrades.
This means that even if we stop and remove the `db` container, the data will still be available in the `vidly` volume, and we can reuse it when we start a new `db` container.

At the very end of the file, we need to declare the named volume `vidly` so that Docker knows to create it and manage its lifecycle. If we don't declare it, Docker will create an anonymous volume that won't be easily manageable or reusable.

In `api` service, we are using a custom entrypoint script (`docker-entrypoint.sh` - in the backend directory) to start the Node.js API server. This script can handle any necessary setup before starting the server, such as waiting for the database to be ready or running migrations. The `command` property overrides the default command defined in the frontend Dockerfile, allowing us to run our custom script instead.

In `web-test` service, we are using the `vidly-web` image (which is built from the `web` service --> image name is `vidly-web`) to run tests for the frontend. We are mounting the `frontend` directory to `/app` inside the container, so that we can run tests against the latest code. The command `npm test` will run the tests defined in the `package.json` file of the React app.
