## 🚀 Starting and Stopping Containers

Managing container lifecycles is a fundamental part of working with Docker. Starting and stopping containers gives you control over which applications are running, when, and where.

---

### ▶️ Starting Containers

You can start containers in different modes depending on your needs.

#### 📦 Start a new container

```bash
docker run --name web-app nginx
```

This starts an `nginx` container named `web-app` and runs it in the foreground.

#### 🌙 Start in detached mode

```bash
docker run -d --name web-app nginx
```

Detached mode runs the container in the background, returning the container ID immediately.

---

### 🔄 Starting Existing Containers

Once a container has been created (but stopped), you can restart it without rebuilding:

```bash
docker start web-app
```

Or restart one that's still running:

```bash
docker restart web-app
```

You can also start all stopped containers:

```bash
docker container start $(docker container ls -aq)
```

---

### ⏹️ Stopping Containers

Gracefully stop a running container:

```bash
docker stop web-app
```

This sends a `SIGTERM` followed by a `SIGKILL` if the container doesn’t exit in 10 seconds.

Force a container to stop immediately:

```bash
docker kill web-app
```

This sends a `SIGKILL` without a grace period.

---

### 🧪 Inspecting Running Containers

List running containers:

```bash
docker ps
```

List all containers (including stopped):

```bash
docker ps -a
```

---

### 🧠 Best Practices

- ✅ Name your containers using `--name` to avoid managing cryptic IDs.
- ✅ Use `-d` for background services, and foreground mode for debugging.
- ✅ Use `docker restart` in scripts or health check loops.
- ⚠️ Avoid `kill` unless absolutely necessary—it may skip cleanup routines.

---

Up next: we’ll take a deeper look at **viewing logs** from running containers to debug and monitor application behavior.

<br>

## 📜 Viewing Logs from Containers

Logs are essential for understanding what’s happening inside a running container. Docker captures everything sent to **stdout** and **stderr**, and makes it accessible via the `docker logs` command.

---

### 🧪 Basic Usage

```bash
docker logs <container_name_or_id>
```

This retrieves all logs from the container since it started.

---

### 🔄 Real-Time Log Streaming

To follow logs as they’re generated:

```bash
docker logs -f <container_name_or_id>
```

This is similar to `tail -f` and is great for live debugging.

---

### ⏱️ Time-Based Filtering

View logs from a specific time:

```bash
docker logs --since 10m <container_name_or_id>
```

You can also use timestamps:

```bash
docker logs --since "2025-07-22T14:00:00" <container_name_or_id>
```

---

### 📉 Tail Logs

Show only the last N lines:

```bash
docker logs --tail 50 <container_name_or_id>
```

Combine with `--follow` to stream from the last few lines:

```bash
docker logs --tail 20 -f <container_name_or_id>
```

---

### 🕒 Add Timestamps

Include timestamps with each log entry:

```bash
docker logs -t <container_name_or_id>
```

---

### 🧠 Best Practices

- ✅ Use `--tail` and `--follow` together for efficient live monitoring.
- ✅ Use `--since` to narrow down logs during incident analysis.
- ✅ Combine with `grep` to search for patterns:
  ```bash
  docker logs <container_name_or_id> | grep "ERROR"
  ```
- ❌ Avoid dumping full logs unless necessary—can be slow and noisy.

---

### 🧪 Log Storage Location (Advanced)

By default, Docker stores logs in JSON format on the host:

```
/var/lib/docker/containers/<container_id>/<container_id>-json.log
```

You can inspect these directly if needed, but prefer using `docker logs`.

---

### 🔧 Logging Drivers

Docker supports multiple logging drivers (e.g., `json-file`, `syslog`, `journald`, `fluentd`). You can configure them per container or globally.

Example:

```bash
docker run --log-driver syslog nginx
```

> 🧠 Use centralized logging for production environments to avoid disk bloat and improve observability.

---

Next up: we’ll explore **publishing ports**—how to expose container services to the host and external networks.

<br>

## 🌐 Publishing Ports from Containers

Publishing ports allows your containerized application to be accessible from outside the Docker network—whether that’s your local machine, another host, or the internet. It’s a key step in connecting your container to the real world.

---

### 🔧 Syntax

```bash
docker run -p <HOST_PORT>:<CONTAINER_PORT> <image>
```

- `HOST_PORT`: Port on your machine (e.g. `8080`)
- `CONTAINER_PORT`: Port inside the container (e.g. `80`)

Example:

```bash
docker run -d -p 8080:80 nginx
```

This maps port `80` inside the container to port `8080` on your host. You can now access the containerized app at `http://localhost:8080`.

---

### 🧪 Publishing Multiple Ports

```bash
docker run -d -p 8080:80 -p 8443:443 nginx
```

This maps both HTTP and HTTPS ports to your host.

---

### 🎲 Ephemeral Port Mapping

Let Docker choose a random host port:

```bash
docker run -d -p 80 nginx
```

Check the assigned port:

```bash
docker ps
```

Output might show:

```
PORTS
0.0.0.0:49153->80/tcp
```

---

### 🔄 Publish All Exposed Ports

If your image uses `EXPOSE`, you can publish all of them automatically:

```bash
docker run -P nginx
```

This maps each exposed port to a random host port.

---

### 🧠 Best Practices

- ✅ Use explicit `-p` mappings for predictable access.
- ✅ Avoid publishing sensitive ports (e.g. databases) unless secured.
- ✅ Use firewalls or reverse proxies to control access.
- ✅ Document port mappings in your README or Compose files.

---

### 🧪 Docker Compose Example

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

This maps container port `80` to host port `8080`.

---

### ⚠️ Security Considerations

- Published ports are accessible from **all network interfaces** by default.
- Use `-p 127.0.0.1:8080:80` to restrict access to localhost.
- Avoid publishing ports for internal services (e.g. databases or caches) unless necessary.

---

Next up: we’ll explore **executing commands inside running containers**—from one-off tasks to interactive debugging.

<br>

## 🛠️ Executing Commands in Running Containers

Sometimes you need to inspect, debug, or interact with a running container—without restarting it or modifying its image. Docker makes this easy with the `exec` and `attach` commands.

---

### 🧪 `docker exec`: Run Commands Inside a Container

The `exec` command lets you run arbitrary commands inside a running container.

#### 📦 Basic Syntax

```bash
docker exec <container_name_or_id> <command>
```

#### 🧭 Examples

- Run a simple command:

  ```bash
  docker exec web-app ls /usr/share/nginx/html
  ```

- Run a shell interactively:

  ```bash
  docker exec -it web-app bash
  ```

  > 🧠 If the container uses Alpine or BusyBox, use `sh` instead of `bash`.

- Run a command as a different user:

  ```bash
  docker exec -u www-data web-app whoami
  ```

- Run multiple commands:
  ```bash
  docker exec -it web-app sh -c "apt-get update && apt-get install -y curl"
  ```

---

### 🔄 `docker attach`: Connect to Main Process

Use `attach` to connect to the container’s primary process (e.g., a running server):

```bash
docker attach web-app
```

> ⚠️ Exiting `attach` may stop the container unless you detach properly (`Ctrl + P`, `Ctrl + Q`).

---

### 🧠 Best Practices

- ✅ Use `exec` for one-off commands and debugging.
- ✅ Use `-it` for interactive sessions.
- ✅ Prefer `exec` over `attach` to avoid disrupting the main process.
- ❌ Don’t rely on `exec` for automation—use Dockerfiles or entrypoints instead.

---

### 🧪 Troubleshooting Tips

- If `exec` fails, make sure the container is running:

  ```bash
  docker ps
  ```

- If the shell doesn’t respond, try using `sh` instead of `bash`.

- Use `docker inspect` to check container details:
  ```bash
  docker inspect web-app
  ```

---

Next up: we’ll explore **removing containers**—how to clean up stopped containers, force removal, and prune unused resources.

<br>

## 🧹 Removing Containers

Cleaning up containers is essential for maintaining a tidy Docker environment. Whether you're freeing up disk space, removing outdated services, or resetting your development setup, Docker provides flexible commands to remove containers safely and efficiently.

---

### 🗑️ Remove a Specific Container

To remove a single container (must be stopped first):

```bash
docker rm <container_name_or_id>
```

Example:

```bash
docker rm web-app
```

---

### 🛑 Stop Before Removing

If the container is still running, stop it first:

```bash
docker stop web-app
docker rm web-app
```

Or force removal (sends SIGKILL):

```bash
docker rm -f web-app
```

> ⚠️ **Use `-f` with caution**—it skips graceful shutdown and may cause data loss.

---

### 🧺 Remove Multiple Containers

You can remove several containers at once:

```bash
docker rm container1 container2 container3
```

Or remove all stopped containers:

```bash
docker container prune
```

> 🧠 This will prompt for confirmation unless you add `-f`.

---

### 🧨 Remove All Containers (Running & Stopped)

```bash
docker rm -f $(docker ps -aq)
```

This forcibly removes every container on the system.

---

### 🧪 Filter by Image

Remove all containers created from a specific image:

```bash
docker ps -a -q --filter ancestor=nginx | xargs docker rm
```

---

### 🧼 Remove Container and Its Volumes

If you want to delete associated anonymous volumes:

```bash
docker rm -v web-app
```

> Named volumes are **not** removed with this command.

---

### 🔍 Inspect Before Removing

List all containers:

```bash
docker ps -a
```

Check container status:

```bash
docker inspect web-app
```

---

### 🧠 Best Practices

- ✅ Use `docker ps -a` to review before removing.
- ✅ Prefer `docker container prune` for routine cleanup.
- ✅ Use `--volumes` to clean up storage when appropriate.
- ❌ Avoid `-f` unless you're sure the container can be killed safely.
- ✅ Automate cleanup in CI/CD pipelines to avoid clutter.

---

Next up: we’ll explore the **container file system**—how it works, how to inspect it, and how to interact with files inside containers.

<br>

## 📁 Understanding the Container File System

Every Docker container has its own **isolated file system**, built from layers of its image. This file system is **read-write** and ephemeral—meaning changes made inside the container don’t affect the image, and are lost when the container is deleted (unless persisted).

---

### 🧬 How It Works

Docker uses a **Union File System** (like OverlayFS) to stack layers:

1. **Base Image Layers**: Read-only layers from the image.
2. **Container Layer**: A writable layer added when the container starts.
3. **Volumes/Mounts**: Optional external storage attached to the container.

> 🧠 Changes made during runtime (e.g. creating files, installing packages) go into the container’s writable layer.

---

### 🔍 Exploring the File System

You can inspect a container’s file system using:

#### 🛠️ `docker exec`

```bash
docker exec -it my_container sh
```

This opens a shell inside the container. You can then use commands like:

```bash
ls /
cd /app
cat /etc/os-release
```

#### 📦 `docker cp`

Copy files between host and container:

```bash
docker cp my_container:/etc/nginx/nginx.conf ./nginx.conf
```

#### 📤 `docker export`

Export the entire container file system:

```bash
docker export my_container > container.tar
```

Then inspect it with:

```bash
tar -tf container.tar
```

---

### 🧪 Inspecting Layers

View image history:

```bash
docker history my_image
```

Inspect container metadata:

```bash
docker inspect my_container
```

---

### 🧠 Best Practices

- ✅ Use volumes for persistent data.
- ✅ Avoid writing to the container layer unless necessary.
- ✅ Use `.dockerignore` to keep builds clean.
- ✅ Use multi-stage builds to separate build-time and runtime layers.

---

Next up: we’ll explore **persisting data using volumes**—how to create, mount, and manage volumes for long-term storage.

<br>

## 💾 Persisting Data Using Volumes

By default, data written inside a Docker container is **ephemeral**—it disappears when the container is stopped and removed. To persist data across container lifecycles, Docker provides **volumes**, which are external storage locations managed by Docker and designed for long-term data persistence.

---

### 📦 What Are Volumes?

Volumes are directories stored on the host system (or cloud) that are **mounted into containers**. They exist independently of containers and can be reused, shared, backed up, and inspected.

---

### 🧪 Creating and Using Volumes

#### 🔹 Create a named volume

```bash
docker volume create mydata
```

#### 🔹 Mount volume into a container

```bash
docker run -d -v mydata:/app/data nginx
```

This mounts the `mydata` volume into `/app/data` inside the container meaning any data written to `/app/data` will persist in `mydata` volume.

> 🧠 If the volume doesn’t exist, Docker will create it automatically.

---

### 🔄 Volume Lifecycle

- Volumes persist **even after containers are deleted**
- You can reuse volumes across multiple containers
- Volumes are stored in:
  ```
  /var/lib/docker/volumes/<volume_name>/_data
  ```

---

### 🧪 Inspecting and Managing Volumes

- List volumes:

  ```bash
  docker volume ls
  ```

- Inspect volume details:

  ```bash
  docker volume inspect mydata
  ```

- Remove a volume:

  ```bash
  docker volume rm mydata
  ```

- Remove all unused volumes:
  ```bash
  docker volume prune
  ```

> ⚠️ Volumes must be **detached** from containers before removal.

---

### 🔗 Sharing Volumes Between Containers

You can mount the same volume into multiple containers:

```bash
docker run -d --name app1 -v shared:/data alpine
docker run -d --name app2 -v shared:/data alpine
```

This enables shared access to `/data` between `app1` and `app2` containers.

---

### 🧪 Persisting Database Data (Example)

```bash
docker volume create pgdata

docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  postgres
```

Even if the container is removed, the database files remain in `pgdata`.

---

### 🧼 Best Practices

- ✅ Use **named volumes** for persistent data
- ✅ Avoid storing data in the container’s writable layer
- ✅ Regularly **backup** important volumes
- ✅ Use `.dockerignore` to exclude volume folders from builds
- ✅ Use **bind mounts** for development, volumes for production

---

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
RUN addgroup appgroup && adduser -S -G appgroup appuser
USER appuser
WORKDIR /app
RUN mkdir data  # Create a directory by user 'appuser' for persistent data - avoids permission issues
COPY package*.json .
RUN npm install
COPY . .
ENV API_URL=http://api.myapp.com/
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

As shown in the example, creating a directory for persistent data inside the container ensures that the user has the right permissions to write to it, avoiding common permission issues when using volumes. If we run a command like `docker run -d -v mydata:/app/data myapp` and we didn't have instruction to create the `/app/data` directory, we might face permission errors because directory would be owned by root.

---

Next up: we’ll explore **copying files between host and containers**—how to move data in and out of containers efficiently.

<br>

## 📤 Copying Files Between Host and Containers

Docker makes it easy to move files between your host system and running containers using the `docker cp` command. This is especially useful for debugging, injecting temporary configs, or extracting logs—without rebuilding images or restarting containers.

---

### 🔁 Basic Syntax

```bash
docker cp <source_path> <container_name_or_id>:<destination_path>
docker cp <container_name_or_id>:<source_path> <destination_path>
```

- You can copy **from host to container** or **from container to host**
- Works with both **files** and **directories**

---

### 📥 Copying from Host to Container

```bash
docker cp ./config.json my_container:/app/config.json
```

This copies `config.json` from your current directory into `/app/` inside the container.

---

### 📤 Copying from Container to Host

```bash
docker cp my_container:/app/logs/output.log ./output.log
```

This pulls `output.log` from the container into your current host directory.

---

### 📁 Copying Entire Directories

```bash
docker cp ./website/ my_container:/var/www/html/
docker cp my_container:/var/www/html/ ./website/
```

> 🧠 Docker will **overwrite existing files** at the destination unless you rename or relocate them.

---

### 🧪 Tips & Limitations

- ✅ Works even if the container is **stopped**
- ❌ You **cannot copy between two containers** directly
- ✅ Use `-a` to preserve file attributes:
  ```bash
  docker cp -a ./data my_container:/app/data
  ```
- ✅ Use `-L` to follow symlinks in the source

---

### 🧼 Best Practices

- ✅ Prefer volumes or bind mounts for persistent or frequent file sharing
- ✅ Use `docker cp` for **one-off transfers**, debugging, or quick edits
- ❌ Avoid relying on `docker cp` in production workflows
- ✅ Document any manual file transfers for reproducibility

---

Next up: we’ll explore **sharing source code with a container** using bind mounts—ideal for live development and syncing changes in real time.

<br>

## 🔗 Sharing Source Code with a Container (Bind Mounts)

When developing locally, it's often useful to share your source code directory with a running container so that changes made on the host are instantly reflected inside the container. This is achieved using **bind mounts**, which link a host directory to a path inside the container.

---

### 📦 What Is a Bind Mount?

A bind mount connects a **specific directory or file on your host** to a **specific location inside the container**. Unlike volumes, bind mounts are ideal for development because they allow **live editing** of code.

---

### 🧪 Basic Syntax

```bash
docker run -v /path/on/host:/path/in/container <image>
```

Or using relative path:

```bash
docker run -v $(pwd):/app my-image
```

This mounts your current working directory into `/app` inside the container.

---

### 🧬 Example: Node.js Development

```bash
docker run -it --rm \
  -v $(pwd):/app \
  -w /app \
  node:18 \
  bash
```

- `-v $(pwd):/app`: Mounts your source code
- `-w /app`: Sets working directory
- `--rm`: Removes container after exit
- `bash`: Opens interactive shell

Now you can run:

```bash
npm install
npm start
```

And any changes you make to files on your host will be reflected instantly inside the container.

---

### 🧪 Docker Compose Example

```yaml
services:
  app:
    image: node:18
    volumes:
      - .:/app
    working_dir: /app
    command: npm start
```

This setup allows you to run your app with `docker compose up` and edit code live.

---

### 🔐 File Permissions

Ensure Docker has access to the host directory. You can set read/write mode:

```bash
-v ./src:/app:rw   # Read-write
-v ./src:/app:ro   # Read-only
```

> 🧠 Use `:ro` for config files or static assets you don’t want modified by the container.

---

### ⚠️ Platform Notes

- On **Windows**, use absolute paths:
  ```bash
  docker run -v C:\Users\Stjepan\project:/app my-image
  ```
- On **macOS/Linux**, `$(pwd)` works well.
- Bind mounts may be slower on Windows/macOS due to VM overhead. Consider using volumes or sync tools for large projects.

---

### 🧼 Best Practices

- ✅ Use bind mounts for live development
- ✅ Avoid mounting sensitive files (use `.dockerignore`)
- ✅ Use volumes for persistent data (e.g., databases)
- ✅ Document mount paths in your README or Compose file
- ❌ Don’t use bind mounts in production unless absolutely necessary

---

Next up: we’ll dive into **Running Multi-Container Applications**—how to orchestrate services using Docker Compose and connect them via networks.
