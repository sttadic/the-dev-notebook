## 📦 Image Management

- `docker pull <image>[:tag]`

  Downloads a Docker image from a remote registry (default: Docker Hub).
  If no tag is specified, `latest` is used.

  **Example:**

  ```bash
  docker pull node:18
  ```

---

- `docker build -t <name>:<tag> <path>`

  Builds a Docker image from a Dockerfile located at `<path>`.
  The `-t` flag tags the image with a name and optional version (tag).

  **Example:**

  ```bash
  docker build -t myapp:1.0 .
  ```

  Note:

  - The `.` at the end specifies the current directory as the build context.
  - The `Dockerfile` should be present in the specified path (current directory in this case).
  - If no tag is specified (in this case it is `1.0`), the default tag `latest` is used.
  - If the image already exists, it will be rebuilt unless the `--no-cache` option is used to force a fresh build.
  - Other common flags include `--build-arg` to pass build-time variables and `--file` to specify a different Dockerfile name.
  - If we omitt the `-t` flag, the image will be built without a tag, which is not recommended as it makes it harder to manage images.

---

- `docker images`

  Lists all locally available Docker images including repository name, tag, image ID, size, and creation date.

---

- `docker rmi <image_id_or_name>`

  Deletes one or more Docker images.
  Cannot remove an image that’s in use by a container unless `-f` (force) is used.

  **Example:**

  ```bash
  docker rmi myapp:1.0
  ```

---

## 📂 Container Management

- `docker run [OPTIONS] <image> [COMMAND] [ARG...]`

  Creates and starts a new container from an image.
  Accepts many useful options (see below).

  **Common Flags:**

  - `-it`: Enables interactive mode with a pseudo-TTY (used for shells).
  - `-d`: Detached mode – runs the container in the background.
  - `--name <name>`: Assigns a custom name to the container.
  - `-p <host_port>:<container_port>`: Maps a container port to a host port.
  - `-v <host_path>:<container_path>`: Mounts a volume or bind mount.

  **Example:**

  ```bash
  docker run -it --name myubuntu ubuntu /bin/bash
  ```

---

- `docker start <container>`

  Starts an **existing, stopped** container (created via `docker run` or `docker create`).
  Does not accept additional flags like ports or volumes — it uses previous configuration.

  **Example:**

  ```bash
  docker start nodeserver
  ```

---

- `docker stop <container>`

  Gracefully stops a **running** container by sending SIGTERM, followed by SIGKILL after a timeout.

---

- `docker restart <container>`

  Stops and then starts a container in one step.

---

- `docker rm <container>`

  Removes a **stopped** container from the system.
  Add `-f` to forcibly stop and remove running containers.

---

- `docker exec -it <container> <command>`

  Executes a command inside a **running** container.
  Commonly used to open an interactive shell (e.g., bash or sh) within a running container.

  **Example:**

  ```bash
  docker exec -it mycontainer /bin/bash   # We can also specify the user that runs this command with -u flag, e.g. 'docker exec -it -u root mycontainer /bin/bash'
  ```

---

- `docker attach <container>`

  Attaches your terminal to a **running container**’s main process.
  This is different from `exec` — it connects to the container’s standard input/output.

---

## 🧠 Container Info & Monitoring

- `docker ps`

  Lists **running** containers along with their names, IDs, status, ports, and base image.

---

- `docker ps -a`

  Lists **all** containers, including stopped or failed ones.

---

- `docker inspect <container_or_image>`

  Returns a detailed JSON output of the low-level configuration of a container or image.

---

- `docker logs <container>`

  Shows the standard output logs (stdout/stderr) of a container.
  Add `-f` to follow logs in real time.

  **Example:**

  ```bash
  docker logs -f myapp
  ```

---

- `docker top <container>`

  Displays running processes inside a container, similar to `ps` on Linux.

---

- `docker stats`

  Shows live CPU, memory, I/O, and network usage for running containers.
  Useful for performance monitoring.

---

## 🗂️ Volumes and Bind Mounts

- `docker volume ls`

  Lists all Docker-managed volumes (named volumes).

---

- `docker volume create <name>`

  Creates a named volume for persistent container data.

---

- `docker volume inspect <volume>`

  Displays low-level metadata and mount paths for a specific volume.

---

- `docker run -v <volume_name>:<container_path> <image>`

  Mounts a **named volume** into a container.

  **Example:**

  ```bash
  docker run -v mydata:/var/lib/mysql mysql
  ```

---

- `docker run -v <host_path>:<container_path> <image>`

  Creates a **bind mount** — links a specific folder from host into the container.

---

- `docker volume prune`

  Removes all **unused** volumes (not attached to any container).

---

## 🌐 Networks

- `docker network ls`

  Lists all Docker networks.

---

- `docker network create <name>`

  Creates a new **user-defined bridge** network.

---

- `docker network inspect <name>`

  Displays detailed network configuration including connected containers and IP addresses.

---

- `docker run --network=<network_name> ...`

  Connects a container to a specific Docker network.

  **Useful for inter-container communication** without exposing to the host.

---

## 🧹 Cleanup and Maintenance

- `docker system prune`

  Cleans up unused containers, networks, images, and volumes (dangerous!).

  Add `-a` to include **unused images**.

---

- `docker image prune`

  Removes **dangling images** (untagged or intermediate).

---

- `docker container prune`

  Removes all **stopped containers**.

---

## 🧰 Docker Compose

- `docker-compose up`

  Starts all services defined in `docker-compose.yml`.

  Add `-d` to start in detached mode.

---

- `docker-compose down`

  Stops all services and removes containers, networks, and volumes created by Compose.

---

- `docker-compose build`

  Builds or rebuilds services from Dockerfiles.
  Add `--no-cache` to force a fresh build without cache. Cache is used by default to speed up builds by caching layers while building images from Dockerfiles.

---

- `docker-compose logs -f`

  Shows real-time logs from all services in the Compose project.

---

> 🧭 **Tip:** You can always use `--help` with any command for full documentation.
> Example: `docker run --help`
