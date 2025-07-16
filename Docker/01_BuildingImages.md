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

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
```

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

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
WORKDIR /app
COPY . .
```

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

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
WORKDIR /app
COPY . .
RUN npm install
```

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

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
WORKDIR /app
COPY . .
RUN npm install
ENV API_URL=http://api.myapp.com/
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

---

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
WORKDIR /app
COPY . .
RUN npm install
ENV API_URL=http://api.myapp.com/
EXPOSE 3000
```

Next up: we’ll explore **setting the user context** with `USER`, why it matters for security, and how to avoid running as root inside containers.

<br>

## 👤 Setting the User Context with `USER`

By default, Docker containers run as **root** (UID 0), which can pose serious security risks. The `USER` instruction lets you specify a **non-root user** to run your application, following the principle of least privilege.

---

### 🔧 Syntax

```Dockerfile
USER <username>
USER <UID>
USER <username>:<groupname>
USER <UID>:<GID>
```

---

### 🛡️ Why Use a Non-Root User?

- ✅ Reduces attack surface
- ✅ Prevents accidental system-level changes
- ✅ Complies with security policies (e.g., OpenShift blocks root containers)
- ✅ Improves container isolation

---

### 🧪 Creating a User in the Dockerfile

You can create a dedicated user during the build:

```Dockerfile
# Create user and group
`RUN groupadd -g 1001 appgroup && \
    useradd -m -u 1001 -g appgroup appuser

# Switch to non-root user
USER appuser
```

> 🧠 **Tip**: Use numeric UID/GID for consistency across distributions.

---

### 🧼 Setting Permissions

Make sure the user has access to necessary directories:

```Dockerfile
RUN mkdir /app && chown appuser:appgroup /app
WORKDIR /app
USER appuser
```

---

### 🔄 Switching Users Temporarily

If you need root for privileged operations:

```Dockerfile
USER root
RUN apt-get update && apt-get install -y some-package
USER appuser
```

> ⚠️ Avoid using `sudo` in Dockerfiles—it’s not recommended and often unsupported.

---

### 🧠 Best Practices

- ✅ Always set a non-root user explicitly
- ✅ Use `USER` after setting up permissions
- ✅ Combine with `WORKDIR` to avoid access issues
- ✅ Avoid hardcoded paths writable only by root
- ✅ Prefer `/tmp` for temporary data (world-writable)

---

### 🧪 Example: Secure Node.js App

```Dockerfile
FROM node:18-slim

# Create user
RUN useradd -m -u 1001 appuser

# Set permissions
RUN mkdir /app && chown appuser /app

# Switch user and set working directory
USER appuser    # Following command will run as user "appuser" from now on
WORKDIR /app

COPY . .
RUN npm install

EXPOSE 3000
CMD ["npm", "start"]
```

---

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
WORKDIR /app
COPY . .
RUN npm install
ENV API_URL=http://api.myapp.com/
EXPOSE 3000
RUN addgroup appgroup && adduser -S -G appgroup app    # Alpine Linux specific command is addgroup and adduser (not groupadd and useradd as in Debian/Ubuntu.)
USER app
```

---

Next up: we’ll explore **defining entrypoints** with `CMD` and `ENTRYPOINT`, how they differ, and how to use them together for flexible container behavior.

<br>

## 🧭 Defining Entrypoints with `ENTRYPOINT` and `CMD`

The `ENTRYPOINT` and `CMD` instructions determine **how your container behaves when it starts**. They define the default executable and its arguments, shaping the container’s runtime behavior.

These two commands are different from `RUN`, which executes commands during the build phase, while `ENTRYPOINT` and `CMD` define the command to run when the container starts (runtime instructions). For example, a command that starts a development server: `npm run dev` (defined in dockerfile as `CMD ["npm", "run", "dev"]`), or a command that runs a Python script: `python app.py` (defined in Dockerfile as `ENTRYPOINT ["python", "app.py"]`).

It is possible not to inculde, for example, `npm run dev` in the Dockerfile, but instead to run it directly when starting the container, like this: `docker run my-app npm run dev`. However, it is recommended to use `CMD` or `ENTRYPOINT` to define the default command to run when the container starts, so that you don't have to specify it every time you run the container.

---

### 🧪 Purpose & Differences

| Instruction  | Role                        | Overridable?             | Typical Use       |
| ------------ | --------------------------- | ------------------------ | ----------------- |
| `ENTRYPOINT` | Defines the main executable | Only with `--entrypoint` | Fixed behavior    |
| `CMD`        | Provides default arguments  | ✅ Yes                   | Flexible defaults |

> 🧠 Use `ENTRYPOINT` for containers that should always run a specific command. Use `CMD` to supply default arguments that can be overridden.

Example:

- If we had following instructions in Dockerfile: `CMD ["npm", "run", "dev"]`, the container would run `node npm run dev` by default. If we wanted to override the command, we could run container initially using additional arguments: `docker run my-app npm run test`, which would run `node npm run test` instead, overriding CMD command in Dockerfile.
- On the other hand, `ENTRYPOINT` can not be overriden (unless we use --entrypont option). So, if we had instructions `ENTRYPOINT ["npm", "run"]` and `CMD ["dev"]`, the container would run `npm run dev` by default, but we could override it with `docker run my-app test`, which would run `npm run test` instead. In this case, `ENTRYPOINT` defines the main command (`npm run`), while `CMD` provides the default argument (`dev`) that can be overridden.

---

### 🔧 Two Syntax Forms of CMD and ENTRYPOINT

#### Shell Form

```Dockerfile
ENTRYPOINT echo "Hello World"
CMD echo "Hello World"
# or from our example:
CMD npm run dev
```

- Runs in a separate shell (shell on linux is usually `/bin/sh -c`, while on windows `cmd.exe /S /C`)
- Allows shell features (e.g., variable expansion)
- Poor signal handling (not PID 1) - signals may not propagate correctly to the process (not recommended for production)

#### Exec Form (array of strings) - preferred

```Dockerfile
ENTRYPOINT ["echo", "Hello World"]
CMD ["--verbose"]
# or from our example:
CMD ["npm", "run", "dev"]
```

- Runs directly as PID 1, no need to spin up a shell
- Better signal handling
- Preferred for production containers
- No shell features (e.g., no variable expansion)
- More predictable behavior
- Makes it easier to clean up processes and handle signals correctly

---

### 🧬 Combining `ENTRYPOINT` and `CMD`

```Dockerfile
FROM python:3.12-slim

WORKDIR /app
COPY . .

ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8080"]
```

- Default command: `python app.py --port 8080`
- Override CMD: `docker run my-app --port 9090`
- Override ENTRYPOINT: `docker run --entrypoint /bin/bash my-app`

---

### 🧪 Real-World Example: Wrapper Script

**entrypoint.sh**

```bash
#!/bin/sh
until nc -z db 5432; do
  echo "Waiting for DB..."
  sleep 2
done
exec python app.py "$@"
```

**Dockerfile**

```Dockerfile
FROM python:3.12-alpine

WORKDIR /app
COPY . .
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
CMD ["--port", "8080"]
```

> 🧠 `exec` replaces the shell with the Python process, ensuring proper signal handling.

---

### 🧼 Best Practices

- ✅ Use **exec form** for both `ENTRYPOINT` and `CMD`
- ✅ Keep `ENTRYPOINT` minimal and predictable
- ✅ Use `CMD` for arguments that users may override
- ✅ Avoid hardcoding secrets or environment-specific values
- ✅ Document expected behavior clearly

---

### 📘 **From Mosh's Docker Course**:

```Dockerfile
FROM node:22.13.1-alpine3.20
RUN addgroup appgroup && adduser -S -G appgroup app
USER app
WORKDIR /app
COPY . .
RUN npm install
ENV API_URL=http://api.myapp.com/
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

Notice that we moved `RUN addgroup appgroup && adduser -S -G appgroup app` and `USER app` intstructions to the top of the Dockerfile, so that they are executed before any other instructions. This is a good practice to ensure that the user and group are created before any files are copied or commands are run, which helps avoid permission issues.

---

Next up: we’ll explore **speeding up builds** with caching strategies, multi-stage builds, and layer optimization.

<br>

## ⚡ Speeding Up Docker Builds

A well-optimized Docker build saves time, resources, and headaches—especially in CI/CD pipelines or large multi-container projects. Below are strategies for minimizing build time and keeping your images fast, clean, and cache-friendly.

---

### 🚀 Layers and Layer Caching

Docker builds images in layers, and each instruction in a Dockerfile (like FROM, COPY, RUN, etc.) creates a new layer in the resulting image.

Each layer is cached and immutable, meaning that if a layer hasn’t changed, Docker can reuse it — speeding up builds.

Docker's layer caching system stores each layer as a snapshot (build cache), and these are stored persistently on your machine, unless explicitly cleared. For example, if you run `docker build -t my-app .`, Docker will create a new image named `my-app` and store the layers in the build cache. If you run the same command again, Docker will check if any layers have changed and reuse the cached layers where possible, speeding up the build process.

Command in Dockerfile like `RUN npm install` will produce a layer with everything that existed before, plus the installed node_modules, and this is snapshotted and stored. If you change a file that is not related to the `npm install` command, Docker will reuse the cached layer for `npm install`, making the build faster.

Docker build process works like this:

- Checks each instruction in your Dockerfile.
- Compares it to the cache:
  - Has it seen this instruction with the exact same context before?
  - Did the files or directories involved change?
- If **yes**, it reuses the cached layer.
- If **no**, it invalidates the cache and rebuilds from that point on.

To check the cache, you can use the `docker history` command to see the layers of an image and their sizes. For example, `docker history my-app` will show you the layers of the `my-app` image, including the size of each layer and when it was created. Note that base instruction like `FROM` might contain multiple layers, depending on the base image and how it was built.

#### ⚠️ Poor caching:

```Dockerfile
COPY . .
RUN npm install
```

If any file changes, even a single line of code is added to any of the files, the `npm install` and all subsequent layers (instructions below it) will have be rebuilt, which can be slow.

> 🧠 Tip: Place commands with frequent changes near the bottom of the Dockerfile.

---

#### 🔄 Good caching example:

```Dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```

Changes to your code won't invalidate the `npm install` layer thus saving time.<br> So, we are first copying only the `package*.json` files that are needed for installing dependencies, then running `npm install`, and finally copying the rest of the application code. This way, if we change any of the application code files, the `npm install` layer, which is usually time expensive, will not be invalidated, and Docker will reuse the cached layer for `npm install`, speeding up the build process.

### 📦 Multi-Stage Builds

Multi-stage builds let you separate build-time dependencies from runtime, reducing final image size.

```Dockerfile
# Stage 1: Build
FROM node:18 as builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Stage 2: Runtime
FROM node:18-slim
WORKDIR /app
COPY --from=builder /app/dist ./dist
RUN npm install --production
CMD ["node", "dist/index.js"]
```

✅ Benefits:

- Smaller, leaner image
- No build tools or source files in final image

---

### 📃 `.dockerignore`

Reduce build context size to speed up upload and build times. Keep unnecessary files out of the image.

> Already covered in a previous section, but **crucial for performance**.

---

### ⚙️ Cache External Dependencies

When downloading remote files (e.g., `curl`, `wget`), save them in cached layers to avoid repeated downloads:

```Dockerfile
RUN curl -O https://example.com/binary && chmod +x binary
```

---

### 🔧 Build Arguments with `ARG`

Use `ARG` to inject build-time values like versions or flags:

```Dockerfile
ARG NODE_VERSION=18
FROM node:$NODE_VERSION
```

> Note: `ARG` values are not persisted in the final image
