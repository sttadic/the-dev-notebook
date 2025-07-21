### 1. Writing a Dockerfile: Base Structure

```Dockerfile
# Use a base image (specify Node version and distro)
FROM node:20-alpine

# Set working directory inside container
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy app source code
COPY . .

# Build app (for React/Node frontends)
RUN npm run build

# Expose port (if running a server inside container)
EXPOSE 3000

# Start app (adjust depending on your app start script)
CMD ["npm", "start"]
```

---

### 2. Building the Docker Image

```bash
docker build -t physio-log-frontend:latest .
```

- `docker build`: Build Docker image from Dockerfile in current directory (`.`)
- `-t physio-log-frontend:latest`: Tag the image for easy reference (name:tag)

---

### 3. Running a Container Locally

```bash
docker run -p 3000:3000 physio-log-frontend:latest
```

- `docker run`: Start a new container
- `-p 3000:3000`: Map host port 3000 to container port 3000 (adjust ports as needed)
- `physio-log-frontend:latest`: Image name to create container from

---

### 4. Viewing Running Containers

```bash
docker ps
```

- Lists all currently running containers (use `docker ps -a` to see all containers, including stopped ones)

---

### 5. Stopping a Container

```bash
docker stop <container_id_or_name>
```

- Stops a running container by ID or name

---

### 6. Removing a Container

```bash
docker rm <container_id_or_name>
```

- Deletes a stopped container to free resources

---

### 7. Pushing Image to Docker Registry

```bash
# Log in to Docker Hub (or your registry)
docker login

# Tag image for your Docker Hub repository
docker tag physio-log-frontend:latest yourdockerhubusername/physio-log-frontend:latest

# Push image to Docker Hub
docker push yourdockerhubusername/physio-log-frontend:latest
```

- Replace `yourdockerhubusername` with your actual Docker Hub username
- Registry hosts your image for sharing and deployment

---

### 8. Pulling Image from Docker Registry

```bash
docker pull yourdockerhubusername/physio-log-frontend:latest
```

- Downloads the image from the registry to your local machine

---

### 9. Multi-stage Build (Example for Smaller Production Image)

```Dockerfile
# Stage 1: Build
FROM node:20-alpine as build

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve (using lightweight nginx)
FROM nginx:alpine

COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

- Builds app in first stage, copies only build output to nginx image
- Results in smaller final image optimized for production

---

### 🐳 More on Multi-Stage Docker Build for Node.js

This Dockerfile demonstrates how to use a multi-stage build to create a lightweight, production-ready Node.js image while leveraging a full-featured environment during the build phase.

---

#### ✅ Why Multi-Stage?

- 🧰 Use a full-featured image (`node:18`, based on Debian) to install and build everything.
- 🧼 Use a minimal image (`node:18-alpine`) for the final container — smaller, faster, more secure.
- 🎯 Only the final stage ends up in the production image.

---

#### 🛠️ Example Dockerfile

```dockerfile
# ---------- Stage 1: Build Stage ----------
FROM node:18 as builder

# Set working directory
WORKDIR /app

# Install all dependencies (including devDependencies)
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# Build the application (e.g., transpile TypeScript, compile assets)
RUN npm run build


# ---------- Stage 2: Production Stage ----------
FROM node:18-alpine as production

# Set working directory
WORKDIR /app

# Copy only the build output and production-related files
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./

# Install only production dependencies
RUN npm install --only=production

# Define the command to run the app
CMD ["node", "dist/index.js"]
```

HOW IT WORKS:

- Docker builds both stages in sequence.
- builder stage compiles and installs everything, then production stage selectively copies only what's needed.
- The final image is based on Alpine Linux → tiny and secure.
- You get the best of both worlds: full build power, minimal runtime size.

RESULTING IMAGE:

- No source files
- No dev dependencies
- No build tools
- Much smaller final image size (~50–100MB)
- Faster startup and safer deployment

BONUS TIPS:

- Add `USER` node in final stage to run as non-root.
- Use `npm ci` instead of `npm install` for deterministic installs (deletes node_modules if exists, always installs exact same versions of every package as in `package-lock.json` - no changes are made to it). Best practice is to use `npm install` in development and `npm ci` in CI/CD pipelines, Docker production builds or anywhere you want repeatable and clean installs
- Combine with `.dockerignore` to skip unnecessary files.

---

🛠️ Updated Multi-Stage Dockerfile for Node.js using best practices:

```dockerfile
# ---------- Stage 1: Build ----------

FROM node:18 as builder

# Set working directory

WORKDIR /app

# Copy package files first to leverage Docker layer caching

COPY package\*.json ./

# Clean, deterministic install of all dependencies (including dev)

RUN npm ci

# Copy the rest of the application source

COPY . .

# Build the application (e.g., transpile TypeScript, bundle React, etc.)

RUN npm run build

# ---------- Stage 2: Production ----------

FROM node:18-alpine as production

# Set working directory

WORKDIR /app

# Install dependencies (you’ll need these tools to run `npm ci`)

RUN apk add --no-cache curl && \
 addgroup -g 1001 nodeapp && \
 adduser -S -u 1001 -G nodeapp nodeapp

# Copy only production dependencies + built output from builder stage

COPY --from=builder /app/package\*.json ./
COPY --from=builder /app/dist ./dist

# Deterministic install of production dependencies only

RUN npm ci --only=production

# Use a non-root user for better security

USER nodeapp

# Start the application

CMD ["node", "dist/index.js"]
```

---

### 10. Helpful Docker Commands

| Command                               | Purpose                                           |
| ------------------------------------- | ------------------------------------------------- |
| `docker images`                       | List all downloaded images                        |
| `docker logs <container>`             | View logs from running container                  |
| `docker exec -it <container> /bin/sh` | Open shell inside running container               |
| `docker system prune`                 | Clean up unused images/containers                 |
| `docker run -it <image>`              | Run container interactively (e.g. running ubuntu) |

---

Keep this cheat sheet handy as you grow your Docker skills!
Feel free to ask if you want deeper dives into any of these steps or more advanced tips. You’re going to crush this! 💪🐳
