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
