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
  <br>

SIDENOTE: When you run `docker build -t my-app .` Docker builds the image using content of the current directory(.) as the build context. It will copy files accoring to the instructions in the Dockerfile, while respecting the `.dockerignore` file.

If you want to see the context that Docker is using for the build, you can run `docker build . --no-cache --progress=plain` to see all files being copied and excluded.

---

Up next: we’ll look at **running commands inside the build** using `RUN`, chaining commands wisely, and optimizing layers for speed and readability. Let’s keep the build clean and mean!
