## 🧰 Basic Linux Commands

These are foundational commands every Linux user should know.

### `pwd` – Print Working Directory

- Shows the full path of the current directory.
- Example:
  ```bash
  pwd
  ```

### `ls` – List Directory Contents

- Lists files and directories in the current location.
- Common variations:
  - `ls -l` : Long listing format (permissions, size, date)
  - `ls -a` : Includes hidden files
  - `ls -lh` : Human-readable sizes
  - `ls -la` : Long listing including hidden files
- Example:
  ```bash
  ls -la
  ```

### `cd` – Change Directory

- Navigates between directories.
- Common usage:
  - `cd /path/to/dir` : Go to specific directory
  - `cd ..` : Move up one level
  - `cd ~` : Go to home directory
  - `cd -` : Return to previous directory
- Example:
  ```bash
  cd ~/Documents
  ```

### `clear` – Clear Terminal Screen

- Clears all previous output from the terminal.
- Example:
  ```bash
  clear
  ```

### `echo` – Display a Line of Text

- Prints text or variables to the terminal.
- Common usage:
  - `echo "Hello"` : Prints Hello
  - `echo $HOME` : Prints value of HOME environment variable
- Example:
  ```bash
  echo "Welcome to Linux"
  ```

### `man` – Manual Pages

- Displays the manual for a command.
- Common usage:
  - `man ls` : Shows documentation for `ls`
- Tip: Use `q` to quit the manual.
- Example:
  ```bash
  man pwd
  ```

### `exit` – Exit the Terminal Session

- Ends the current shell session.
- Example:
  ```bash
  exit
  ```

### `whoami` – Show Current User

- Displays the username of the current user.
- Example:
  ```bash
  whoami
  ```

### `date` – Show Current Date and Time

- Displays the system date and time.
- Example:
  ```bash
  date
  ```

### `cal` – Display Calendar

- Shows a calendar for the current month.
- Common usage:
  - `cal` : Current month
  - `cal 2025` : Full year calendar
- Example:
  ```bash
  cal
  ```

### `history` – Command History

- Displays a list of previously executed commands.
- Common usage:
  - `history` : Shows command history
  - `!n` : Executes command number `n` from history

<br>

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
