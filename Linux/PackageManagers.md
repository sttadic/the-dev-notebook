## 📦 Package Management with `apt` and `apt-get`

These commands are used to manage software packages on Debian-based systems like Ubuntu and Linux Mint.

### 🔄 `apt update` – Update Package Index

- Refreshes the list of available packages and versions.
- Does **not** install or upgrade any packages.
- Example:
  ```bash
  sudo apt update
  ```

### ⬆️ `apt upgrade` – Upgrade Installed Packages

- Installs the newest versions of all currently installed packages.
- Does **not** remove or install new packages to satisfy dependencies.
- Example:
  ```bash
  sudo apt upgrade
  ```

### 🚀 `apt full-upgrade` – Upgrade with Dependency Changes

- Like `upgrade`, but also removes/install packages if needed to resolve dependencies.
- Example:
  ```bash
  sudo apt full-upgrade   # same as 'sudo apt dist-upgrade'
  ```

### 📥 `apt install` – Install New Packages

- Installs one or more packages.
- Common variations:
  - `sudo apt install package1 package2` : Install multiple packages
  - `sudo apt install package=version` : Install a specific version
  - `--no-upgrade` : Don’t upgrade if already installed
  - `--only-upgrade` : Only upgrade, don’t install if not present
- Example:
  ```bash
  sudo apt install curl
  ```

### 🧹 `apt remove` – Remove Installed Packages

- Removes package binaries but keeps config files.
- Example:
  ```bash
  sudo apt remove nano
  ```

### 🧼 `apt purge` – Remove Package and Config Files

- Completely removes package and its configuration.
- Example:
  ```bash
  sudo apt purge nano
  ```

### 🧽 `apt autoremove` – Clean Up Unused Dependencies

- Removes packages installed as dependencies that are no longer needed.
- Example:
  ```bash
  sudo apt autoremove
  ```

### 🧾 `apt list` – List Packages

- Shows installed, upgradeable, or all packages.
- Examples:
  ```bash
  apt list --installed
  apt list --upgradable
  ```

### 🔍 `apt search` – Search for Packages

- Searches package names and descriptions.
- Example:
  ```bash
  apt search firefox
  ```

### 📖 `apt show` – Show Package Details

- Displays detailed info about a package.
- Example:
  ```bash
  apt show git
  ```

---

### 🧠 `apt` vs `apt-get`

| Feature         | `apt`                        | `apt-get`                |
| --------------- | ---------------------------- | ------------------------ |
| Introduced      | Ubuntu 16.04+                | Older, traditional tool  |
| User-friendly   | Yes (progress bars, cleaner) | More script-friendly     |
| Recommended for | Interactive use              | Scripting and automation |
| Example         | `apt install`                | `apt-get install`        |

> 💡 Tip: Use `apt` for day-to-day tasks and `apt-get` for scripting or when you need more granular control.

<br>

## 🧱 Working with `dpkg` – Debian Package Manager

`dpkg` is a low-level tool for installing, removing, and inspecting `.deb` packages. It doesn’t resolve dependencies automatically like `apt` does.

### 📦 Install a `.deb` Package

- Installs a local `.deb` file.
- Example:
  ```bash
  sudo dpkg -i package-name.deb
  ```

### 🧹 Remove a Package (Keep Config Files)

- Removes the package but leaves config files.
- Example:
  ```bash
  sudo dpkg -r package-name
  ```

### 🧼 Purge a Package (Remove Everything)

- Removes the package and its config files.
- Example:
  ```bash
  sudo dpkg -P package-name
  ```

### 📋 List Installed Packages

- Shows all installed packages.
- Example:
  ```bash
  dpkg -l
  ```

### 🔍 Show Package Info

- Displays details about an installed package.
- Example:
  ```bash
  dpkg -s package-name
  ```

### 📁 List Files Installed by a Package

- Shows all files installed by a package.
- Example:
  ```bash
  dpkg -L package-name
  ```

### 🔎 Find Which Package Owns a File

- Identifies the package that installed a specific file.
- Example:
  ```bash
  dpkg -S /path/to/file
  ```

### 📦 Unpack Without Configuring

- Extracts files from a `.deb` without configuring.
- Example:
  ```bash
  sudo dpkg --unpack package-name.deb
  ```

> ⚠️ Use `dpkg` when you need direct control. For everyday package management, stick with `apt`.

<br>

## 📦 Snap & Flatpak – Universal Package Formats

Snap and Flatpak are modern, cross-distro packaging systems. They bundle applications with their dependencies and run them in isolated environments (sandboxes). This makes them ideal for desktop apps and systems where stability and compatibility are key.

---

### 🔹 Snap – Canonical’s Universal Package System

Snap is developed by Canonical (Ubuntu’s parent company) and is **pre-installed on Ubuntu**.

#### Common Snap Commands:

```bash
# Install a snap package
sudo snap install <package-name>

# Remove a snap package
sudo snap remove <package-name>

# List installed snaps
snap list

# Find a snap package
snap find <search-term>

# Show info about a snap
snap info <package-name>

# Update all snaps
sudo snap refresh
```

> 🧠 Snap apps are stored in `/snap` and run in a sandbox. They auto-update and work across many distros.

---

### 🔸 Flatpak – A Cross-Distro Alternative

Flatpak is popular on Fedora and other non-Ubuntu distros, but it works on Ubuntu too (after installing it).

#### Setup (Ubuntu):

```bash
# Install Flatpak
sudo apt install flatpak

# Add Flathub (main Flatpak repo)
sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

#### Common Flatpak Commands:

```bash
# Install a Flatpak app
flatpak install flathub <App-ID>

# Run a Flatpak app
flatpak run <App-ID>

# List installed apps
flatpak list

# Update all Flatpak apps
flatpak update

# Remove an app
flatpak uninstall <App-ID>
```

> 💡 Flatpak apps are sandboxed and stored under `~/.var/app/` or `/var/lib/flatpak`. They’re great for running multiple versions of the same app.

---

### 🆚 Snap vs Flatpak – Quick Comparison

| Feature           | Snap                         | Flatpak                         |
| ----------------- | ---------------------------- | ------------------------------- |
| Default on Ubuntu | ✅ Yes                       | ❌ No (needs install)           |
| Main repo         | Snap Store                   | Flathub                         |
| Sandboxed         | ✅ Yes                       | ✅ Yes                          |
| Auto-updates      | ✅ Yes                       | ❌ Manual                       |
| GUI integration   | Good on Ubuntu               | Good with GNOME plugin          |
| Ideal for         | System & CLI tools, GUI apps | GUI apps, dev/test environments |

> ✨ Use Snap for system tools and Ubuntu-native apps. Use Flatpak when you want the latest desktop apps or cross-distro compatibility.
