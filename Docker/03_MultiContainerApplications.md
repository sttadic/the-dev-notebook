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

### 📘 **From Mosh's Docker Course**:

- Note that our app is named `vidly` (root directory) and consists of a frontend, backend, and database (frontend and backend are subdirectories). The frontend is a React app, the backend is a Node.js API, and the database is MongoDB.

```yaml
version: "3.9"

services:
  web:
    build: ./frontent
    ports:
      - "3000:3000"
  api:
    build: ./backend
    ports:
      - "3001:3001"
    environment:
      DB_URL: mongodb://db/vidly
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
