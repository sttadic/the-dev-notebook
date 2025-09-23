# AWS EC2 – Quick Reference Notes

## 1. What is EC2?

- **Amazon EC2 (Elastic Compute Cloud)** provides scalable virtual servers in the cloud.
- Think of it as _renting computers on demand_ from AWS’s data centers.
- You choose:
  - OS (Linux, Windows, etc.)
  - CPU, memory, storage, networking
- **Benefits:**
  - Pay only for what you use
  - Easily scale up/down
  - Integrates with other AWS services

**Analogy:**

- Traditional servers = buying a physical computer
- EC2 = renting virtual computers on-demand

---

## 2. AMI (Amazon Machine Image)

- **Template** used to launch EC2 instances.
- Contains:
  - OS
  - Preinstalled software
  - Configurations
- Immutable → doesn’t change once created.

**Analogies:**

- Java: `Class → Object`
- Docker: `Image → Container`

Example AMI: <br>
al2023-ami-2023.8.20250915.0-kernel-6.1-x86_64

- al2023 = Amazon Linux 2023
- 2023.8.20250915.0 = version & build date (version 2023.8, built Sept 15, 2025)
- kernel-6.1 = Linux kernel version
- x86_64 = architecture (64-bit Intel/AMD)
- Default SSH user = ec2-user

---

## 3. Architectures: x86 vs Arm (Graviton)

- **x86 (Intel/AMD)**

  - Traditional desktop/server architecture
  - Broad compatibility, especially for legacy/commercial software
  - Slightly more expensive

- **Arm (AWS Graviton)**
  - Started in mobile/embedded → now optimized for cloud
  - Up to 20–40% cost savings
  - Better performance per watt
  - Requires software compatibility (sometimes recompilation)

**Analogy:**

- x86 = reliable pickup truck (works everywhere, higher fuel cost)
- Arm = electric car (efficient, modern, but check infrastructure/software)

---

## 4. Instance Types (families)

Choose based on workload:

- **General Purpose** (e.g., `t3`, `t4g`, `m6i`)

  - Balanced CPU/memory/network
  - Good for dev/test, small-medium apps

- **Compute Optimized** (e.g., `c6i`, `c7g`)

  - More CPU power
  - Great for CPU-heavy tasks (batch jobs, game servers, HPC)

- **Memory Optimized** (e.g., `r6i`, `x2g`)

  - High RAM
  - Best for databases, in-memory caching, analytics

- **Storage Optimized** (e.g., `i3`, `i4i`)

  - High IOPS / throughput
  - For data warehousing, NoSQL, Hadoop

- **Accelerated Computing** (e.g., `p4`, `g5`, `inf1`)
  - GPUs / FPGAs
  - For ML, 3D rendering, HPC

---

## 5. Key Pairs (SSH/RDP login)

- AWS way to securely log in.
- **Key pair = Public key + Private key**
  - Public key → stored on instance
  - Private key (`.pem`) → downloaded once, you keep it safe
- Login flow:
  - You prove identity with private key
  - Instance verifies with public key
- Much safer than passwords.

**Tips:**

- Only one chance to download `.pem`
- Secure permissions: `chmod 400 mykey.pem`
- Without it, you need alternate access (e.g., Session Manager)

---

## 6. Session Manager (AWS Systems Manager)

- Browser-based terminal in AWS Console
- No SSH keys required
- Needs IAM role + permissions
- Good for quick practice
- Still, SSH key pairs are closer to real-world workflows

---

## 7. Networking & Security Basics

- **VPC/Subnet:** where the instance “lives”
- **Security Groups:** act as firewalls
  - Allow inbound ports (e.g., 22 for SSH, 80/443 for web)
- **Public IP:** required for direct internet access

---

## 8. Storage

- **EBS Volumes** → default storage for EC2
- Common types:
  - gp3/gp2 (general purpose SSD)
  - io1/io2 (provisioned IOPS)
- Defaults (8 GB gp2/gp3) are fine for practice

---

## 9. SSH into EC2 from WSL2

1. Copy `.pem` key into WSL2:

   ```bash
   cp /mnt/c/Users/<WinUser>/Downloads/mykey.pem ~/.ssh/
   chmod 400 ~/.ssh/mykey.pem
   ```

2. Find Public IPv4 of instance in AWS Console.

3. Connect using:

   ```bash
   ssh -i ~/.ssh/mykey.pem ec2-user@<PublicIPv4>
   ```

   - Replace `ec2-user` with appropriate user for your AMI (e.g., `ubuntu` for Ubuntu AMIs).

4. If connection fails, check:

   - Security Group inbound rules (port 22 open)
   - Instance state (running)
   - Public IP address

5. To exit SSH session, type `exit` or `logout` or `Ctrl + D`.

---

## 10. Stopping vs Terminating Instances

- **Stopping** an instance:
  - Instance is shut down but not deleted
  - EBS volumes remain, data persists
  - You can restart it later
  - You are charged for EBS storage while stopped
- **Terminating** an instance:
  - Instance is deleted permanently
  - EBS volumes (unless marked "Delete on Termination") are deleted
  - You cannot restart it; you must launch a new instance
  - No further charges for the instance, but you may still be charged for EBS storage

---

## 11. SSH Clients Comparison

- WSL2 SSH

  - Pure Linux experience
  - Best for scripting/automation

- MobaXterm

  - Windows GUI with SSH + file browser + tabs
  - Can run remote GUI apps

- PuTTY

  - Lightweight, classic
  - Requires converting .pem → .ppk
  - No extras by default

- Recommendation:
  - Use WSL2 for authentic Linux workflow
  - MobaXterm if you want GUI perks
  - PuTTY only if you need something minimal

---

## 12. First Steps After Connecting

Check system info:

```bash
uname -a
cat /etc/os-release
```

Update packages:

```bash
sudo dnf update -y
```

Optional: host a simple app/web server to test networking

```bash
sudo dnf install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "Hello from EC2" | sudo tee /var/www/html/index.html
```

- Open port 80 in Security Group to access via browser
