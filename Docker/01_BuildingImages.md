## 📝 Defining the Dockerfile

A **Dockerfile** is a plain text file containing a series of instructions that Docker uses to build a container image. Each instruction creates a new layer in the image.
This layered architecture allows for efficient storage and reuse of image components, making Docker images lightweight and fast to deploy.

It's created within the root of your project directory and named `Dockerfile` (no file extension).

### 📐 Basic Structure

Here’s a simple example Dockerfile:

```Dockerfile
# Base image
FROM node:18

# Set working directory
WORKDIR /app

# Copy application code
COPY package*.json ./
RUN npm install
COPY . .

# Expose port and define default command
EXPOSE 3000
CMD ["npm", "start"]
```

### 📌 Common Dockerfile Instructions

| Instruction    | Description                                                                                                             |
| -------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `FROM`         | Sets the base image (must be the first instruction)                                                                     |
| `WORKDIR`      | Specifies the working directory inside the container (all follwing commands will be exectued within this directory)     |
| `COPY` / `ADD` | Copies files/directories into the image (COPY is preferred over ADD because it’s more explicit)                         |
| `RUN`          | Executes a command during image build (e.g., installing dependencies, e.g. `RUN apt update && apt install -y curl`)     |
| `ARG`          | Defines a variable that users can pass at build-time to the Docker command (e.g., `ARG VERSION=1.0`)                    |
| `VOLUME`       | Creates a mount point with the specified path and marks it as holding externally mounted volumes (e.g., `VOLUME /data`) |
| `LABEL`        | Adds metadata to the image (e.g., `LABEL maintainer="`)                                                                 |
| `ENV`          | Sets environment variables                                                                                              |
| `EXPOSE`       | Declares port the container will use (informational; does not publish the port)                                         |
| `CMD`          | Sets the default command when the container runs (can be overridden at runtime)                                         |
| `ENTRYPOINT`   | Specifies a fixed command to run; often used in combination with `CMD` (e.g., `ENTRYPOINT ["python"]`)                  |
| `USER`         | Specifies which user to run the container as (default is root)                                                          |

> 🧠 **Tip**: Use `COPY` instead of `ADD` unless you explicitly need `ADD`'s ability to extract archives or fetch remote URLs.

### 🧼 Best Practices

- **Pin versions** of base images to avoid unexpected updates.
- Group related `RUN` instructions using `&&` to minimize image layers:
  ```Dockerfile
  RUN apt-get update && apt-get install -y \
      curl \
      git
  ```
- **Order matters**: Place frequently changing files (like `package.json`) later in the Dockerfile to leverage caching.
- Use `.dockerignore` to avoid copying unnecessary files (we’ll explore this more soon).

---

Next up, we’ll look at **choosing the right base image**—a deceptively impactful decision. Ready when you are!

<br>

## 🧱 Choosing the Right Base Image

The `FROM` instruction defines the base image for your Dockerfile. It lays the groundwork for everything that follows. Selecting the right image depends on your use case, runtime requirements, and size/security constraints.

---

### 🧩 Types of Base Images

| Type                | Description                                                                                     |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| **Official images** | Curated by Docker, maintained regularly, great starting point (e.g. `node`, `python`, `golang`) |
| **Distro-based**    | Full Linux distributions like `ubuntu`, `alpine`, or `debian`                                   |
| **Slim variants**   | Lighter versions of official images (e.g. `python:3.12-slim`)                                   |
| **Scratch**         | An empty image used for ultra-minimal, static builds (e.g. with Go or Rust)                     |
| **Custom**          | Your own images built from scratch or extended from other images                                |

---

### 📦 Popular Examples

```Dockerfile
# Node.js app with full image
FROM node:18

# Minimal Node.js image
FROM node:18-slim

# Python application base
FROM python:3.12-alpine

# Minimal Go binary with no base
FROM scratch
COPY my-binary /my-binary
ENTRYPOINT ["/my-binary"]
```

If we were to use `From node:18.17.1-alpine3.18`, it would be a minimal Node.js image based on Alpine Linux, which is lightweight and efficient.
So, if we run command `docker build -t my-node-app .` - it would create a Docker image named `my-node-app` using the specified Alpine-based Node.js image.<br>
Then, we could run it in interactive mode with `docker run -it my-node-app`, which would start a container from the `my-node-app` image, allowing us to interact with Node.js directly in the terminal (e.g. runnig javascript commands, etc.).<br>
If we wanted to run a linux (alpine) shell, we could use `docker run -it my-node-app sh`, which would start a container from the Alpine 3.18 image and give us a shell prompt to interact with the Alpine Linux environment. It would have Node.js installed, but we could use it to run any Linux commands as well (e.g. just like in a regular Alpine Linux environment).

---

### 🎯 Tips for Base Image Selection

- ✅ **Use slim or Alpine variants** to reduce image size:
  - `node:18-slim` or `python:3.12-alpine`
- ⚠️ **Beware of Alpine with some packages** — musl libc (used by Alpine) may cause incompatibilities for some Python or Node packages.
- 🔒 **Smaller images = fewer vulnerabilities** — reduce your attack surface.
- 🧪 **Test for build compatibility** — especially when using slim or Alpine variants.

---

### 🧠 Best Practices

- Stick to **official images** when possible. Official images can be found on [Docker Hub](https://hub.docker.com/).
- Pin versions precisely to avoid changes:
  ```Dockerfile
  FROM node:18.17.1-slim
  ```
- Keep image hierarchy clear if you're building multi-stage images (more on that soon!).

---

Next: we’ll get into **copying files**, using `.dockerignore`, and how build caching works with file operations. Let’s keep layering this image the right way.

<br>

## 📂 Copying and Excluding Files & Directories

When building an image, Docker needs to include only the relevant files. Efficiently handling file transfers not only keeps your image size down but also improves build performance.

---

### 🧰 `COPY` vs `ADD`

| Instruction | Description                                                                                   | Notes                                        |
| ----------- | --------------------------------------------------------------------------------------------- | -------------------------------------------- |
| `COPY`      | Copies files/directories from the context                                                     | Prefer for simple, transparent usage         |
| `ADD`       | Same as `COPY`, but with extra features (e.g. auto-extracting tarballs, fetching remote URLs) | Use with caution; behavior may be surprising |

Example of `COPY`:

```Dockerfile
# Copies everything from current context to /app in container
COPY . /app

# Copies specific files to /app directory (more controlled copying)
COPY packge.json README.md /app/

# More controlled copying using wildcards/pattern (will copy all files starting with "package" and ending with ".json")
COPY package*.json ./

# In the cases there is a file containing a space in its name, you can use quotes or square brackets
COPY "file with space.txt" /app/
COPY [ "file with space.txt", "/app/" ]
```

> ⚠️ **Best practice**: Use `COPY` by default. Use `ADD` only if you need its extra features explicitly.

Examples of setting a working directory first with `WORKDIR` and copying files:

```Dockerfile
# Set working directory
WORKDIR /app

# Following command will copy files from the current context (all files in the directory where the Dockerfile is located) to the /app directory in the container
COPY . .
```

Examples of `ADD`:

```Dockerfile
# If there is a file named e.g. archive.tar.gz, it will be automatically extracted into the /app directory (if working directory is set to /app)
ADD . .

# We can add a file from a remote URL
ADD https://example.com/file.txt /app/file.txt

# If you add a compressed file (like .tar.gz), it will be automatically extracted (uncompressed) into the specified directory
ADD archive.tar.gz /app/
```

---

### 🚫 `.dockerignore` — Your Best Friend

Just like `.gitignore`, `.dockerignore` tells Docker which files and folders to ignore during the build. This helps:

- Prevent bloating your image
- Avoid copying sensitive files (e.g. `.env`, `node_modules`, `__pycache__`)
- Speed up the build by reducing context size

#### Example `.dockerignore`:

```

node*modules
.env
Dockerfile
.git
*.log
\_.md

```

---

### 🚀 Best Practices

- Place `COPY package*.json ./` _before_ copying the entire project:

  ```Dockerfile
  COPY package*.json ./
  RUN npm install
  COPY . .
  ```

This optimizes Docker’s layer caching—ensuring dependencies aren’t reinstalled unnecessarily.

- Only copy what you need. Large folders (like test fixtures or image assets) can inflate image size if you’re not careful.

- Run `docker build . --no-cache` occasionally to validate that `.dockerignore` is doing its job (especially before deployment).
  <br><br>

SIDE NOTE: When you run `docker build -t my-app .` Docker builds the image using content of the current directory (" . ") as the build context. It will copy files accoring to the instructions in the Dockerfile, while respecting the `.dockerignore` file.

If you want to see the context that Docker is using for the build, you can run `docker build . --no-cache --progress=plain` to see all files being copied and excluded.

---

Up next: we’ll look at **running commands inside the build** using `RUN`, chaining commands wisely, and optimizing layers for speed and readability.

<br>

## 🛠️ Running Commands with `RUN`

The `RUN` instruction executes commands inside the image **at build time**. It’s commonly used to install packages, configure environments, or compile code. Each `RUN` creates a new image layer, so optimizing its usage is key to keeping images lean and builds fast.

Layer is a fundamental concept in Docker, where each instruction in the Dockerfile creates a new layer in the image. Layers are cached, so if you change a layer, only that layer and subsequent layers need to be rebuilt, which speeds up the build process.

If, for example, for a base image we use `FROM node:18.17.1-alpine3.18`, the first layer will be the base image itself, and then each subsequent `RUN` instruction will create a new layer on top of that base image.

Note that layers are immutable, meaning once created, they cannot be changed. If you need to modify a layer, you must create a new layer on top of it.

Each RUN command will actually run within a Alpine Linux shell (or the shell of the base image), so you can use shell commands, pipes, and environment variables. If we had the following line in Dockerfile: `RUN apt install python3`, and the base image is Alpine Linux, there would be an error since Alpine uses `apk` package manager, not `apt`. So, we need to be careful to use correct commands for the base image we are using.

---

### 🧪 Syntax Options

There are two forms of `RUN`:

- **Shell form** (uses `/bin/sh -c`):

  ```Dockerfile
  RUN apt-get update && apt-get install -y curl
  ```

- **Exec form** (no shell, no variable expansion):
  ```Dockerfile
  RUN ["apt-get", "install", "-y", "curl"]
  ```

> ✅ **Best practice**: Use shell form for complex commands with pipes or environment variables. Use exec form for predictable, signal-safe execution.

---

### 🧹 Chaining Commands

To reduce image layers and improve caching, chain related commands:

```Dockerfile
RUN apt-get update && \
    apt-get install -y curl git && \
    rm -rf /var/lib/apt/lists/*
```

> 🧠 **Why chain?** Each `RUN` creates a new layer. Chaining avoids unnecessary intermediate layers and keeps your image smaller.

---

### 🧼 Clean Up After Yourself

Always remove temporary files and package caches:

```Dockerfile
RUN apt-get update && \
    apt-get install -y build-essential && \
    rm -rf /var/lib/apt/lists/*
```

This prevents bloated images and reduces security risks.

---

### 🧠 Tips & Best Practices

- Combine `RUN` instructions where possible to reduce layers.
- Use `--no-install-recommends` with `apt-get` to avoid unnecessary packages:
  ```Dockerfile
  RUN apt-get install -y --no-install-recommends curl
  ```
- Use `&&` to chain commands, ensuring the build fails if any command fails.
- Avoid installing tools you don’t need in production (e.g., editors, debuggers).
- Use multi-stage builds to separate build-time dependencies from runtime (we’ll cover this later).

---

### 🧪 Example: Installing Node.js on Debian

```Dockerfile
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs && \
    rm -rf /var/lib/apt/lists/*
```

This example installs Node.js on a Debian-based image, ensuring the package list is updated, Node.js is installed, and temporary files are cleaned up to keep the image size down.

---

> Next up: we’ll explore **setting environment variables** with `ENV` and how to use them effectively across your Dockerfile and containers.

<br>

## 🌿 Setting Environment Variables with `ENV`

The `ENV` instruction in a Dockerfile defines **environment variables** that persist in the image and are available at **runtime**. These variables are useful for configuration, portability, and separating code from environment-specific values.

---

### 🔧 Syntax

```Dockerfile
ENV <key>=<value> ...
```

You can define multiple variables in one line or across multiple lines:

```Dockerfile
ENV NODE_ENV=production
ENV PORT=3000
ENV API_URL=http://api.myapp.com/    # E.g. this could be an API for talking to a backend service
```

---

### 🧪 Using `ENV` in Practice

```Dockerfile
FROM node:18
WORKDIR /app

# Set environment variables
ENV NODE_ENV=production \
    PORT=3000

COPY . .
RUN npm install

EXPOSE $PORT
CMD ["npm", "start"]
```

These variables can be accessed in your app like:

- **Node.js**: `console.log(process.env.NODE_ENV)`
- **Python**: `print(os.environ['PORT'])`
- **Java**: `System.getenv("PORT")`
- **Shell**: `echo $PORT` or `printenv PORT`

---

### 🧠 Best Practices

- ✅ **Use `ENV` for runtime configuration** like ports, modes, or feature flags.
- ❌ **Avoid hardcoding secrets** (e.g., passwords, tokens). Use runtime injection instead.
- 🧪 **Reference `ENV` values** in later Dockerfile instructions:
  ```Dockerfile
  ENV APP_HOME=/usr/src/app
  WORKDIR $APP_HOME
  ```

---

### 🔄 Overriding `ENV` at Runtime

You can override `ENV` values when running a container:

```bash
docker run -e PORT=8080 my-app
```

Or use an `.env` file:

```env
PORT=8080
NODE_ENV=development
```

```bash
docker run --env-file .env my-app
```

---

### 🧬 `ENV` vs `ARG`

| Feature          | `ENV`                        | `ARG`                             |
| ---------------- | ---------------------------- | --------------------------------- |
| Scope            | Available at build & runtime | Build-time only                   |
| Persist in image | ✅ Yes                       | ❌ No                             |
| Override at run  | ✅ Yes (`-e`, `--env-file`)  | ❌ No                             |
| Use case         | App config, ports, flags     | Build-time options (e.g. version) |

---

### 🧼 Tips

- Use uppercase names with underscores: `MY_APP_PORT`
- Group related variables together for clarity (e.g., all database settings, API keys, etc.). Example:
  ```Dockerfile
  ENV DB_HOST=db.example.com \
      DB_PORT=5432 \
      DB_USER=myuser \
      DB_PASSWORD=mypassword
  ```
- Document expected variables in your README or Dockerfile comments. Example:
  ```Dockerfile
  # Environment variables:
  # - NODE_ENV: Application environment (default: production)
  # - PORT: Port the app listens on (default: 3000)
  ```

---

Next up: we’ll explore **exposing ports** with `EXPOSE`, what it really does (and doesn’t do), and how it fits into container networking.

<br>

## 🌐 Exposing Ports with `EXPOSE`

The `EXPOSE` instruction in a Dockerfile is used to **document** which ports the containerized application is expected to listen on at runtime. It’s a signal to users and orchestration tools—not a command that actually publishes the port.

---

### 🔧 Syntax

```Dockerfile
EXPOSE <port> [<protocol>]
```

- `<port>`: The internal container port your app listens on.
- `<protocol>`: Optional. Defaults to `tcp`. You can also specify `udp`.

```Dockerfile
EXPOSE 80
EXPOSE 443/tcp
EXPOSE 514/udp
```

---

### 🧠 What `EXPOSE` Does (and Doesn’t Do)

| Action                         | `EXPOSE` Does            | `EXPOSE` Doesn’t             |
| ------------------------------ | ------------------------ | ---------------------------- |
| Document container ports       | ✅                       |                              |
| Enable inter-container comms   | ✅ (via Docker networks) |                              |
| Publish ports to host          |                          | ❌ (use `-p` or `--publish`) |
| Make app accessible externally |                          | ❌                           |

> 🧪 To actually make a port accessible from your host, use `-p` or `--publish` when running the container:
>
> ```bash
> docker run -p 8080:80 my-app
> ```

---

### 🧭 Best Practices

- ✅ **Use `EXPOSE` to document intent**: It helps others understand which ports your app uses.
- ✅ **Use `EXPOSE` with Docker Compose**: Compose can automatically map exposed ports.
- ❌ **Don’t rely on `EXPOSE` alone**: It won’t make your app reachable from outside the container.
- ✅ **Use environment variables** for dynamic port configuration:
  ```Dockerfile
  ENV APP_PORT=3000
  EXPOSE $APP_PORT
  ```

---

### 🧪 Example in Context

```Dockerfile
FROM node:18
WORKDIR /app

COPY . .
RUN npm install

ENV PORT=3000
EXPOSE $PORT

CMD ["npm", "start"]
```

Then run:

```bash
docker build -t my-app .
docker run -p 8080:3000 my-app
```

Now your app is accessible at `http://localhost:8080`.

---

Next up: we’ll explore **setting the user context** with `USER`, why it matters for security, and how to avoid running as root inside containers.
