## 🚀 Introduction

This guide walks through deploying a Dockerized application on a **single host**, such as a virtual private server (VPS). It assumes your app is already containerized and you're ready to move it from development to production. While there are many deployment strategies—like Swarm or Kubernetes—we're keeping it simple and focused here. This step-by-step setup is perfect for small-scale production environments or staging instances.

> In future guides, we'll dig deeper into multi-host orchestration platforms like Docker Swarm and Kubernetes.

<br>

## 🛠️ Deployment Options Overview

Before we roll up our sleeves, it's helpful to understand the broader container deployment landscape:

### 👤 Single-Host Deployment

- Runs all containers on a single server
- Great for small production apps, prototypes, and staging
- Simple to manage, debug, and scale vertically

### 🐝 Docker Swarm (Multi-Host Deployment)

- Built into Docker
- Clusters multiple machines into one virtual Docker host
- Handles service replication, load balancing, rolling updates
- Easier than Kubernetes but with fewer features

### ☸️ Kubernetes (Multi-Host Deployment)

- Advanced container orchestration platform
- Ideal for high-availability and large-scale apps
- Supports autoscaling, service discovery, secrets, monitoring
- Complex to set up and manage

> 💡 **This guide focuses only on single-host deployment.** Swarm and Kubernetes will be covered in separate, dedicated guides so we can give them the attention they deserve.

<br>

## 🧳 Virtual Private Servers (VPS)

A **Virtual Private Server (VPS)** is a remote machine that gives you root-level access in a shared physical environment. You can install Docker, deploy services, and configure firewalls just like a traditional server — but without managing actual hardware.

### 💻 What a VPS Gives You

- **Root access**: Full control over system resources and configuration
- **Persistent hosting**: Your application is online 24/7
- **Scalability**: Can be resized as needed (CPU, RAM, storage)
- **Affordability**: Typically cheaper than full dedicated servers

### ☁️ Popular VPS Providers

| Provider                   | Highlights                                                   |
| -------------------------- | ------------------------------------------------------------ |
| **DigitalOcean**           | Developer-friendly interface, good docs (used in this guide) |
| **Linode**                 | Balanced pricing, strong community                           |
| **Hetzner**                | Very affordable in EU regions                                |
| **Vultr**                  | Many global datacenter locations                             |
| **AWS Lightsail**          | Simplified VPS offering from Amazon                          |
| **Oracle Cloud Free Tier** | Free VPS (limited resources) — great for testing             |

> 📌 Choose a provider based on proximity to users, budget, and preferred control panel or interface.

### 🛠️ Recommended VPS Specs for Docker Hosting

Minimum for typical web apps:

- **OS**: Ubuntu 22.04 LTS (stable & Docker-friendly)
- **RAM**: 1–2 GB (4 GB+ for heavier stacks)
- **CPU**: 1–2 cores (more for compute-intensive apps)
- **Storage**: SSD for faster I/O, at least 20 GB
- **Networking**: Public IPv4, SSH access, firewall configuration

> 🧠 Many VPS plans start around $5/month and scale up depending on your needs.

We'll walk through provisioning, connecting, and configuring your VPS next. Ready to dive in?

<br>

## 🔌 Provisioning & Connecting to DigitalOcean VPS

Since we're using **DigitalOcean** for this guide, here's how to get your VPS (called a "droplet" in their terminology) up and running.

### 🏗️ Creating the Droplet

1. Go to [DigitalOcean Control Panel](https://cloud.digitalocean.com/)
2. Click **Create → Droplets**
3. Choose:
   - **OS**: Ubuntu 22.04 LTS (recommended)
   - **Plan**: Start with the $4 or $6 plan (1 GB RAM, 1 vCPU is fine for small apps)
   - **Datacenter Region**: Pick one close to your users
   - **Authentication**: Use SSH key (generate one if needed)
   - **Hostname**: Pick something descriptive like `my-app-server`
   - **Enable Backups** (optional but recommended)

Once created, you'll get a public IP address.

---

### 🔑 Connecting via SSH

Use the terminal to log in:

```bash
ssh root@your_droplet_ip
```

Make sure your SSH key is added locally via:

```bash
ssh-add ~/.ssh/id_your_key_name
```

> ✅ You’ll land in the root shell of your new server. From here, you can install Docker and configure your app.

---

### 🔐 Post-Setup Hardening (Optional but recommended)

- Create a new user (avoid using root daily):
  ```bash
  adduser deploy
  usermod -aG sudo deploy
  ```
- Add SSH key to the new user with commands:

  ```bash
    mkdir -p /home/deploy/.ssh && echo "your-ssh-public-key" >> /home/deploy/.ssh/authorized_keys
    chown -R deploy:deploy /home/deploy/.ssh
    chmod 700 /home/deploy/.ssh
    chmod 600 /home/deploy/.ssh/authorized_keys
  ```

- Disable root SSH login via `/etc/ssh/sshd_config`:
  ```bash
  PermitRootLogin no
  ```
- Install UFW firewall:
  ```bash
  apt install ufw
  ufw allow OpenSSH
  ufw allow 80,443/tcp
  ufw enable
  ```

Ready to install Docker next? Let’s move on.

<br>

## 🐳 Installing Docker on the VPS (modern approach to now retired Docker Machine)

With your DigitalOcean droplet live and ready, the next step is installing **Docker Engine** so your VPS can run containers directly.

---

### 🔧 Manual Docker Installation (Preferred)

Run the official install script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Add your user to the `docker` group (if you created one earlier):

```bash
sudo usermod -aG docker deploy
```

Activate group membership:

```bash
newgrp docker
```

Verify Docker is running:

```bash
docker version
docker info
```

---

### 🤖 What About Docker Machine?

**Docker Machine** is a CLI tool that provisions Docker-ready hosts on cloud providers, including DigitalOcean. It’s handy for automation, CI pipelines, or quick testing—but it's no longer actively maintained and considered **legacy**. Users are encouraged to use **Docker Desktop** or **Docker Engine** directly on supported platforms. Machine's approach to creating and configuring hosts has been superseded by more modern workflows that integrate more closely with Docker Desktop.

#### Docker Machine Overview:

| Feature           | Docker Machine                 | Manual VPS Setup            |
| ----------------- | ------------------------------ | --------------------------- |
| Provisioning      | Automated (via CLI flags)      | Manual via provider UI      |
| Remote Docker CLI | Built-in via context switching | Manual via SSH              |
| Maintenance       | Legacy tool (deprecated)       | Fully supported             |
| Flexibility       | Good for quick experiments     | Better for real deployments |

If you're interested, a typical setup looks like:

```bash
docker-machine create \
  --driver digitalocean \
  --digitalocean-access-token <your_token> \
  my-droplet
```

But for this guide, we’ll stick with the **manual setup**, which is more stable and future-proof.

---

Next up: we'll define your **production configuration** using Docker Compose. Time to tune your stack for life beyond localhost 🎛️

<br>

## 📦 Configuring Production with Docker Compose

Now that Docker is installed and humming along, it's time to orchestrate your app using **Docker Compose**. This tool lets you define services, networks, and volumes in a single YAML file, simplifying deployment and scaling - already covered in the [Multi Container Apps Guide](../Docker/03-MultiContainerApps.md).

---

### 📁 Setting Up `docker-compose.yml`

Here’s a typical production-oriented setup:

```yaml
version: "3.8"

services:
  web:
    image: nginx:stable
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    restart: always
    networks:
      - webnet

  app:
    build: ./app
    environment:
      - NODE_ENV=production
    ports:
      - "3000:3000"
    restart: unless-stopped
    networks:
      - webnet

networks:
  webnet:
```

### 🚀 Deplying the Stack

- Bring up your stack:

  ```bash
  docker compose -f docker-compose.yml up -d
  ```

  This command starts your services in detached mode, allowing them to run in the background. -f flag specifies the compose file to use.

- Monitor containers:

  ```bash
  docker compose ps
  ```

- View logs:

  ```bash
  docker compose logs -f
  ```

- Scale services if needed:

  ```bash
  docker compose up -d --scale app=3
  ```

  This command scales the `app` service to 3 replicas.

### 🧼 Keeping Things Clean

- To stop and remove your stack:

  ```bash
  docker compose down
  ```

- This removes containers but not volumes. If you want a clean slate run:

  ```bash
  docker compose down --volumes
  ```

### 📘 **From Mosh's Docker Course**:

```yaml
services:
  web:
    depends_on:
      - api
    build: ./frontend
    ports:
      - 80:3000
    restart: unless-stopped

  api:
    depends_on:
      - db
    build: ./backend
    ports:
      - "3001:3001"
    environment:
      DB_URL: mongodb://db/vidly
    command: ./docker-entrypoint.
    restart: unless-stopped

  db:
    image: mongo:4.0-xenial
    ports:
      - "27017:27017"
    volumes:
      - vidly:/data/db # MongoDB by default uses /data/db for data storage
    restart: unless-stopped

volumes:
  vidly:
```

---

Next, we'll cover domain setup and optional HTTPS using **Let's Encrypt** and **Nginx** for secure access. Your VPS is starting to look sharp 😎

<br>

## 🌐 Domain Setup + HTTPS with Nginx & Let's Encrypt

To make your VPS accessible from the web and secure it with HTTPS, we’ll configure **Nginx** as a reverse proxy and use **Let's Encrypt** for SSL certificates.

---

### 🏷️ Point Your Domain to Your VPS

In your domain registrar’s DNS settings:

- Create an `A` record pointing to your VPS’s IP.
  Example:

  - Name: `@` or `yourdomain.com`
  - Type: `A`
  - Value: `<VPS_IP_ADDRESS>`

- (Optional) Create a second `A` record for `www` to cover `www.yourdomain.com`

---

### 🔒 Install Certbot + Nginx Plugin

Run:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

---

### ⚙️ Configure Nginx for Your App

Create a site configuration file:

```bash
sudo nano /etc/nginx/sites-available/yourdomain.com
```

Paste this reverse-proxy block:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    location / {
        proxy_pass http://localhost:3000; # Adjust to your app's port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_cache_bypass $http_upgrade;

    }
}
```

Save and exit the editor.

Enable and test it:

```bash
sudo ln -s /etc/nginx/sites-available/yourdomain.com /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

### 🧙‍♂️ Run Certbot for HTTPS

Execute:

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

This will:

- Obtain SSL certificates from Let's Encrypt
- Auto-configure Nginx for HTTPS
- Set up HTTP→HTTPS redirection
- Install a systemd timer to renew certificates automatically

---

### 🧪 Test Your Setup

Visit `https://yourdomain.com` and verify the browser’s lock icon 🔒
For a deep dive, run an SSL report at https://www.ssllabs.com/ssltest/

---

> And voilà! Your VPS is now publicly accessible, encrypted, and production-grade.
> Next up: monitoring, backups, or horizontal scaling

<br>

## 🐞 Troubleshooting Deployment Issues

Even the best‐planned deployments hit bumps. Here’s a structured approach to identify and resolve common problems on your single‐host Docker setup.

---

### 1. Check Service Status & Logs

- **List running containers**

  ```bash
  docker compose ps
  ```

- **Stream all logs**

  ```bash
  docker compose logs -f
  ```

- **Inspect a specific container**

  ```bash
  docker inspect <service-name>
  ```

- **View exit codes**
  ```bash
  docker compose ps --services --filter "status=exited"
  ```

---

### 2. Validate Your Compose File

Catch syntax or schema errors before they bite you in prod:

```bash
docker compose config
```

- Ensures your `docker-compose.yml` merges overrides and variables correctly.
- Highlights missing environment vars, duplicate ports, or invalid keys.

---

### 3. Common Error Patterns

1. **Port Conflicts**

   - Symptoms: “Bind for 0.0.0.0:80 failed”
   - Fix: Change host port or stop the service using it:
     ```bash
     sudo lsof -i :80
     ```

2. **Volume Mount Failures**

   - Symptoms: “Mounts denied”, read‐only errors
   - Fix: Check file paths, permissions, and SELinux/AppArmor rules.

3. **Environment Variable Issues**

   - Symptoms: Application crashes on startup, missing config
   - Fix: Confirm `.env.prod` loaded, run inside container:
     ```bash
     docker compose exec app env | grep VAR_NAME
     ```

4. **DNS / Networking Glitches**
   - Symptoms: “Service not found”, connection timeouts
   - Fix: Ensure services share the same Compose network, test via:
     ```bash
     docker compose exec app ping db
     ```

---

### 4. Inspect Host Resources

- **Disk space**

  ```bash
  df -h
  docker system df
  ```

- **Memory & CPU**

  ```bash
  free -m
  top
  ```

- **Open file/socket limits**
  ```bash
  ulimit -n
  ```

---

### 5. Restart & Clean-Up

Sometimes a stale resource causes havoc:

```bash
docker compose down --volumes --remove-orphans
docker system prune --volumes --force
docker compose up -d
```

> Caution: `prune` deletes all unused containers, networks, images, and volumes.

---

### 6. Debug with a Shell

Drop into a container to poke around:

```bash
docker compose exec app sh
# or bash if available
docker compose exec app bash
```

Inspect file structure, run your app’s launch command manually, and watch for errors in real time.

---

Ready to move on to **publishing updates** and **rolling out new versions** next? 😎

<br>

## 🚀 Publishing Updates & Rolling Out New Versions

Once your fixes or new features are ready, follow these steps to update running services with minimal downtime.

---

### 1. Build Updated Images

- Build all services with new tags defined in `docker-compose.yml`:
  ```bash
  docker compose build
  ```
- Or build and tag explicitly:
  ```bash
  docker build -t myrepo/myapp:1.2.0 .
  ```

### 2. Push to Container Registry

- Login if required:
  ```bash
  docker login myregistry.com
  ```
- Push updated image(s):
  ```bash
  docker push myrepo/myapp:1.2.0
  ```

### 3. Update Compose Configuration

- Change the image tag in `docker-compose.yml`:

  ```yaml
  services:
    web:
      image: myrepo/myapp:1.2.0
  ```

- Or use an environment variable in your .env file for flexibility:
  ```Dotenv
  WEB_IMAGE=myrepo/myapp:1.2.0
  ```
  And then reference it in `docker-compose.yml`:
  ```yaml
  services:
    web:
      image: ${WEB_IMAGE}
  ```

### 4. Rolling Update with Docker Compose

- Rebuild and restart a single service without downtime:
  ```bash
  docker compose up -d --no-deps --build web
  ```
  --no-deps ensures dependencies are not restarted, only the specified service.
- Or pull new images and restart all services:
  ```bash
  docker compose pull
  docker compose up -d
  ```

### 5. Zero-Downtime Strategies (Optional)

Zero-downtime deployments ensure users experience no interruptions.

- Pre-scale new replicas before removing old ones:

  ```bash
  docker compose up -d --scale web=3
  docker compose up -d
  docker compose scale web=2
  ```

- Rely on health checks to ensure readiness:

  ```yaml
  services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 15s
      start_period: 30s
      retries: 3
  ```

### 6. Rollback if Needed

- Revert to a previous tag in docker-compose.yml:
  ```yaml
  services:
    web:
      image: myrepo/myapp:1.1.0
  ```
- Redeply the old version:

  ```bash
  docker compose pull
  docker compose up -d

  ```

### 7. Monitor Post-Deployment

- Check logs for errors:
  ```bash
  docker compose logs -f
  ```
- Verify service health:
  ```bash
  docker compose ps
  ```
- Test critical paths in your application to ensure everything works as expected.

<br>

## 🧹 Cleaning Up Old Resources

After multiple deployments, your Docker host can accumulate unused images, containers, and volumes. Regular cleanup helps maintain performance and free up disk space.

### 1. Remove Unused Containers

```bash
docker container prune
```

### 2. Remove Unused Images

```bash
docker image prune
```

### 3. Remove Unused Volumes

```bash
docker volume prune
```

### 4. Remove Unused Networks

```bash
docker network prune
```

### 5. Comprehensive Cleanup

For a more aggressive cleanup, you can use:

```bash
docker system prune
```

This command removes all stopped containers, unused networks, dangling images, and build cache. Use with caution as it will delete everything not currently in use.

### 6. Scheduled Cleanup (Optional)

To automate cleanup, consider adding a cron job:

```bash
0 3 * * * /usr/bin/docker system prune -f --volumes
```

This example runs the cleanup every day at 3 AM. Adjust the timing as needed.

---
