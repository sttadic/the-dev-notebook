## 📚 Table of Contents

- [🧰 Basic Linux Commands](#-basic-linux-commands)
- [📁 File and Directory Management](#-file-and-directory-management)
- [📝 Viewing and Editing Files](#-viewing-and-editing-files)
- [🔁 Input, Output, and Error Redirection](#-input-output-and-error-redirection)
- [🔍 Searching for Text in Files](#-searching-for-text-in-files)
- [🗂️ Finding Files and Directories](#-finding-files-and-directories)
- [🔗 Pipes (`|`) – Connecting Commands Together](#-pipes---connecting-commands-together)
- [🌍 Environment Variables in Linux](#-environment-variables-in-linux)
- [⚙️ Process Management in Linux](#-process-management-in-linux)
- [👥 User and Group Management in Linux](#-user-and-group-management-in-linux)
- [🔐 File Permissions and Ownership in Linux](#-file-permissions-and-ownership-in-linux)

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

## 📁 File and Directory Management

These commands help you create, move, copy, rename, and delete files and directories in Linux.

---

### 📄 `touch` – Create Empty Files

- Creates a new empty file or updates the timestamp of an existing one.
- Example:

  ```bash
  touch newfile.txt
  touch newfile1.txt newfile2.txt  # Create multiple files at once

  touch -c existingfile.txt  # Only updates timestamp if file exists
  touch -a file.txt  # Update access time only
  touch -m file.txt  # Update modification time only
  touch -t 202501010000 file.txt  # Set specific timestamp (YYYYMMDDhhmm)

  ```

---

### 📂 `mkdir` – Make Directories

- Creates one or more directories.
- Common variations:
  - `mkdir dir1` : Create a single directory
  - `mkdir -p parent/child` : Create nested directories
- Example:
  ```bash
  mkdir -p ~/Projects/LinuxGuide
  ```

---

### 🗑️ `rm` – Remove Files or Directories

- Deletes files or directories.
- Common variations:

  - `rm file.txt` : Delete a file
  - `rm file*` : Delete all files starting with "file" (pattern matching)
  - `rm -r dir/` : Recursively delete a directory and its contents
  - `rm -f file.txt` : Force delete without confirmation
  - `rm -rf dir/` : Force recursive delete (⚠️ dangerous!)
  - `rm -i file.txt` : Prompt before each deletion
  - `rm -v file.txt` : Verbose output (shows what is being deleted)

- Example:
  ```bash
  rm -rf ~/Downloads/old_stuff
  ```

---

### 📋 `cp` – Copy Files and Directories

- Copies files or directories.
- Common variations:
  - `cp file1.txt file2.txt` : Copy file1 to file2
  - `cp file.txt /path/` : Copy file to directory
  - `cp -r dir1/ dir2/` : Copy directories recursively
  - `cp -i` : Prompt before overwrite
- Example:
  ```bash
  cp -r ~/Templates ~/Backup/Templates
  ```

---

### 🚚 `mv` – Move or Rename Files and Directories

- Moves or renames files and directories.
- Common variations:
  - `mv file.txt /path/` : Move file to directory
  - `mv oldname.txt newname.txt` : Rename file
  - `mv -i` : Prompt before overwrite
- Example:
  ```bash
  mv report.txt ~/Documents/Reports/2025_report.txt
  ```

---

### 🔗 `ln` – Create Links

- Creates hard or symbolic (soft) links.
- Common variations:
  - `ln file link` : Hard link
  - `ln -s target link` : Symbolic link
- Example:
  ```bash
  ln -s /usr/bin/python3 ~/python   # Creates a symlink to Python so you can run it with `~/python`
  ```

---

### 📜 `stat` – Show File or Directory Info

- Displays detailed metadata (size, permissions, timestamps).
- Example:
  ```bash
  stat myfile.txt
  ```

---

### 🧾 `file` – Identify

- Tells you what kind of data a file contains.
- Example:
  ```bash
  file image.png   # Outputs: image/png
  ```

---

### 🧹 `basename` and `dirname`

- `basename` strips directory path, leaving just the filename.
- `dirname` strips filename, leaving just the directory path.
- Examples:

  ```bash
  basename /home/stjepan/file.txt   # Outputs: file.txt
  dirname /home/stjepan/file.txt    # Outputs: /home/stjepan
  ```

  <br>

## 📝 Viewing and Editing Files

This section covers commands to **view**, **navigate**, and **edit** text files directly from the terminal — essential for working with logs, configs, and scripts.

---

### 🔍 Viewing Files

#### `cat` – Concatenate and Display File Contents

- Displays the entire content of a file.
- Best for small files.
- Example:
  ```bash
  cat filename.txt
  cat file1.txt file2.txt  # Concatenate multiple files and display their contents together
  ```

#### `less` – View File One Page at a Time

- Scrollable viewer for large files.
- Use `q` to quit, `/search` to find text.
- Example:
  ```bash
  less /var/log/syslog
  ```

#### `more` – Basic Pager (Older Alternative to `less`)

- Similar to `less`, but with fewer features.
- Example:
  ```bash
  more filename.txt
  ```

#### `head` – View First Lines of a File

- Shows the first 10 lines by default.
- Example:
  ```bash
  head filename.txt
  head -n 20 filename.txt  # First 20 lines
  ```

#### `tail` – View Last Lines of a File

- Shows the last 10 lines by default.
- Use `-f` to follow file updates (great for logs).
- Example:
  ```bash
  tail -f /var/log/syslog
  ```

#### `nl` – Numbered Lines

- Like `cat`, but adds line numbers.
- Example:
  ```bash
  nl filename.txt
  ```

---

### ✏️ Editing Files

#### `nano` – Simple Terminal Text Editor

- Beginner-friendly with on-screen shortcuts.
- Save: `Ctrl + O`, Exit: `Ctrl + X`
- Example:
  ```bash
  nano filename.txt
  ```

#### `vim` – Powerful Modal Editor

- Advanced editor with steep learning curve.
- Modes: `i` (insert), `Esc` (command), `:wq` (save & quit)
- Example:
  ```bash
  vim filename.txt
  ```

#### `gedit` – GUI Text Editor (Ubuntu Default)

- Graphical editor for GNOME desktop.
- Example:
  ```bash
  gedit filename.txt &
  ```

#### `sed` – Stream Editor for Inline Edits

- Great for automated find-and-replace.
- Example:
  ```bash
  sed 's/old/new/g' filename.txt
  ```

#### `awk` – Pattern Scanning and Processing

- Ideal for extracting and formatting text.
- Example:
  ```bash
  awk '{print $1}' filename.txt
  ```

---

> 💡 Tip: Use `less` for large files, `nano` for quick edits, and `vim` when you want full control.

<br>

## 🔁 Input, Output, and Error Redirection

Redirection allows you to control where input comes from and where output goes — instead of just reading from the keyboard and writing to the screen.

---

### 📤 Output Redirection (`>` and `>>`)

#### `>` – Redirect Standard Output (Overwrite)

- Sends output to a file, replacing its contents.
- Example:
  ```bash
  echo "Hello" > hello.txt
  cat file.txt > new_file.txt  # Overwrites new_file.txt with contents of file.txt (if new_file.txt doesn't exist, it will be created)
  cat file1.txt file2.txt > combined.txt  # Concatenate multiple files and save to combined.txt
  ```

#### `>>` – Redirect Standard Output (Append)

- Appends output to the end of a file.
- Example:
  ```bash
  echo "World" >> hello.txt
  ```

---

### 📥 Input Redirection (`<`)

#### `<` – Redirect Standard Input

- Feeds a file as input to a command.
- Example:
  ```bash
  wc -l < hello.txt
  ```

---

### ⚠️ Error Redirection (`2>` and `2>>`)

#### `2>` – Redirect Standard Error (Overwrite)

- Sends error messages to a file.
- Example:
  ```bash
  ls nonexistentfile 2> error.log
  ```

#### `2>>` – Redirect Standard Error (Append)

- Appends error messages to a file.
- Example:
  ```bash
  ls nonexistentfile 2>> error.log
  ```

---

### 🔀 Redirect Both Output and Error

#### `&>` – Redirect stdout and stderr (Overwrite)

- Sends both output and errors to the same file.
- stdout (standard output) is file descriptor 1, stderr (standard error) is file descriptor 2.
- Example:
  ```bash
  command &> combined.log   # Redirects both stdout and stderr to combined.log
  ```

#### `&>>` – Redirect stdout and stderr (Append)

- Appends both output and errors to the same file.
- Example:
  ```bash
  command &>> combined.log
  ```

#### `2>&1` – Redirect stderr to stdout

- Useful when combining with other redirections.
- Example:
  ```bash
  command > output.log 2>&1
  ```

---

### 🧪 Bonus: Discard Output

#### `/dev/null` – The "black hole" of Linux

- Discards output or errors.
- Use it to suppress output you don't need.
- Examples:
  ```bash
  command > /dev/null        # Discard stdout
  command 2> /dev/null       # Discard stderr
  command &> /dev/null       # Discard both
  ```

> 💡 Tip: Redirection is especially useful in scripts, cron jobs, and when logging output or suppressing noise.

<br>

## 🔍 Searching for Text in Files

Linux provides powerful tools to search for text within files and directories. The most common and versatile tool is `grep`, but others like `find`, `awk`, and `sed` also play a role in more advanced scenarios.

---

### 📌 `grep` – Global Regular Expression Print

`grep` searches for lines in files that match a given pattern.

#### 🔹 Basic Usage

```bash
grep "pattern" filename
```

- Searches for `"pattern"` in `filename` and prints matching lines.

#### 🔹 Case-Insensitive Search

```bash
grep -i "pattern" filename
```

#### 🔹 Show Line Numbers

```bash
grep -n "pattern" filename
```

#### 🔹 Match Whole Words Only

```bash
grep -w "word" filename
```

#### 🔹 Invert Match (Show Non-Matching Lines)

```bash
grep -v "pattern" filename
```

#### 🔹 Recursive Search in Directories

```bash
grep -r "pattern" /path/to/dir
```

#### 🔹 Show Only Matching Text

```bash
grep -o "pattern" filename
```

#### 🔹 Count Matching Lines

```bash
grep -c "pattern" filename
```

---

### 🧠 Regular Expressions with `grep`

- `.` : Any single character
- `*` : Zero or more of the previous character
- `^` : Start of line
- `$` : End of line
- `[]` : Character class
- `\b` : Word boundary (with `-P` for Perl regex)

Example:

```bash
grep -E "^Error.*[0-9]{3}$" logfile.txt
```

---

### 🧾 Contextual Search

- `-A N` : Show N lines **after** match
- `-B N` : Show N lines **before** match
- `-C N` : Show N lines **before and after**

Example:

```bash
grep -C 2 "timeout" config.yaml
```

---

### 📁 Search Multiple Files

```bash
grep "pattern" file1.txt file2.txt
grep "pattern" *.log
```

---

### 🔍 Combine with `find` for Advanced Search

```bash
find /var/log -name "*.log" -exec grep "error" {} +
```

- Searches for `"error"` in all `.log` files under `/var/log`.

---

### ⚡ Bonus: `ripgrep` (`rg`) – Fast Recursive Search

If installed:

```bash
rg "pattern"
```

- Faster than `grep -r`, ignores binary files, respects `.gitignore`.

---

> 💡 Tip: Use `grep -rni` for a case-insensitive recursive search with line numbers — perfect for scanning codebases or logs.

> <br>

## 🗂️ Finding Files and Directories

Linux offers powerful tools to locate files and directories based on name, type, size, time, permissions, and more. The most commonly used tools are `find`, `locate`, and `which`.

---

### 🔍 `find` – Search Files and Directories Recursively

`find` is a versatile command that searches through directory trees in real time.

#### 🔹 Basic Syntax

```bash
find [path] [options] [expression]
```

#### 🔹 Find by Name

```bash
find /path/to/search -name "filename.txt"    # Exact match
find /path/to/search -iname "filename.txt"   # Case-insensitive match
find /path/to/search -iname "*.jpg"          # All JPG files
```

#### 🔹 Find by Type

```bash
find . -type f            # Regular files
find . -type d            # Directories
find . -type l            # Symbolic links
find . -type f -name "s*" > list.txt    # Find files from current directory (and subdirs) starting with 's' and save to list.txt
```

#### 🔹 Find by Size

```bash
find . -size +10M         # Larger than 10MB
find . -size -1k          # Smaller than 1KB
```

#### 🔹 Find by Modification Time

```bash
find . -mtime -7          # Modified in last 7 days
find . -mmin -30          # Modified in last 30 minutes
```

#### 🔹 Find by Permissions

```bash
find . -perm 644          # Exact permission
find . -perm -u+x         # User has execute permission
```

#### 🔹 Find by Owner or Group

```bash
find /var/www -user www-data
find /home -group developers
```

#### 🔹 Execute Command on Found Files

```bash
find . -name "*.log" -exec rm {} \;
find . -type f -exec chmod 644 {} \;
```

#### 🔹 Delete Files Matching Criteria

```bash
find . -name "*.tmp" -delete
```

#### 🔹 Limit Search Depth

```bash
find . -maxdepth 2 -name "*.conf"
```

---

### 📁 `locate` – Fast File Search Using a Database

`locate` uses a prebuilt index (updated via `updatedb`) for lightning-fast searches.

#### 🔹 Examples

```bash
locate filename.txt
locate "*.pdf"
```

> ⚠️ May not reflect recent changes unless `sudo updatedb` is run.

---

### 📌 `which` – Find Executable Location in PATH

```bash
which python3
```

- Shows the full path of an executable in your `$PATH`.

---

> 💡 Tip: Use `find` for real-time, flexible searches. Use `locate` for speed. Use `which` to find installed commands.

<br>

## 🔗 Pipes (`|`) – Connecting Commands Together

We can chain commands using `;`, `&&`, or `||`. Examples:

```bash
mkdir newdir; cd newdir; touch file.txt  # Create a directory, change into it, and create a file
mkdir newdir && cd newdir && touch file.txt  # Same as above, but only if each command succeeds (since newdir already exists, the rest of the commands will not run)
mkdir newdir || echo "Directory already exists"  # Create a directory, or print a message if it already exists
```

But the most powerful way to connect commands is with **pipes** (`|`), which allow you to pass the output of one command directly into another command as input.

A **pipe** in Linux (`|`) allows you to **take the output of one command and use it as the input for another**. This is incredibly powerful for chaining simple tools into complex workflows — without creating temporary files.

---

### 🧠 How It Works

- Every command has:

  - **stdin** (standard input)
  - **stdout** (standard output)
  - **stderr** (standard error)

- A pipe connects the **stdout of one command** to the **stdin of the next**:

  ```bash
  command1 | command2
  ```

- Think of it like a conveyor belt: `command1` produces data, and `command2` consumes it.

---

### 🔧 Basic Examples

#### Filter files with `grep`

```bash
ls -l | grep ".txt"
```

- Lists only `.txt` files from the current directory.

#### Count lines in a file

```bash
cat file.txt | wc -l
```

- Counts the number of lines in `file.txt`.

#### View logs with search

```bash
dmesg | grep -i error
```

- Shows only lines containing "error" (case-insensitive) from system logs.

---

#### List files in bin directory and pipe to `less` for scrolling

```bash
ls /bin | less
```

- Lists all files in `/bin` and allows you to scroll through them.

### 🧪 Multi-Stage Pipelines

You can chain multiple pipes:

```bash
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -nr
```

- This finds the IPs that caused the most 404 errors in a web server log.

---

### 🧰 Common Commands Used with Pipes

| Command         | Purpose                               |
| --------------- | ------------------------------------- |
| `grep`          | Filter lines by pattern               |
| `awk`           | Extract and format columns            |
| `cut`           | Cut specific fields                   |
| `sort`          | Sort lines alphabetically/numerically |
| `uniq`          | Remove duplicate lines                |
| `wc`            | Count lines, words, characters        |
| `tee`           | Output to file **and** screen         |
| `head` / `tail` | Show first/last lines                 |

---

### 🧼 Best Practices

- Use **quotes** around patterns to avoid shell expansion.
- Use `xargs` when a command doesn’t accept piped input directly.
- Break long pipelines into multiple lines using `\` for readability.

---

> 💡 Pipes are unidirectional (left to right) and ephemeral — they exist only while the command runs. They’re one of the most elegant features of Unix philosophy: _“Do one thing well, and combine tools to do more.”_

<br>

## 🌍 Environment Variables in Linux

Environment variables are dynamic values that affect the behavior of processes and the shell. They store configuration settings like user info, system paths, and preferences — and are inherited by child processes.
Environment variables are crucial for configuring applications, scripts, and the shell itself. They allow you to customize your Linux experience without modifying system files directly.

---

### 📋 Viewing Environment Variables

#### `printenv` – Print Environment Variables. Purpose is to display the values of current environment variables.

```bash
printenv            # List all environment variables
printenv PATH       # Show value of environment variable PATH (this coulb be achieved with `echo $PATH` as well)
```

Note: PATH is a special environment variable that contains a list of directories where the shell looks for executable files.

#### `env` – Show or Run with Custom Environment. Purpose is to display (without arguments) or run a command with a modified environment.

```bash
env                 # List all environment variables
env VAR=value cmd   # Run `cmd` with a temporary variable
```

#### `set` – Show All Shell Variables (incl. functions)

```bash
set | less          # View all shell + environment variables
```

Note:<br>
`Shell` variables are local to the current shell session (e.g. Bash, Zsh) and are not inherited by child processes (by programs of subshells started from this current shell). Often used for temporary settings or script variables (scripting, counters, temporary flags, etc).<br>
`Environment` variables are global and inherited by child processes (e.g. programs, scripts) started from the current shell. Used for configuration settings, paths, and user preferences (to configure system behavior, e.g.: PATH, HOME, USER, etc). Set using `export` command.

#### `echo` – Display a Variable’s Value

```bash
echo $HOME
```

---

### 🧪 Creating and Exporting Variables

#### Shell Variable (Temporary, Local)

```bash
MY_VAR="hello"
echo $MY_VAR
```

#### Export as Environment Variable (Temporary, Global)

```bash
export MY_VAR="hello"
```

> 🧠 Only exported variables are inherited by child processes.

---

### 🔁 Temporary Environment for a Command

```bash
MY_VAR="value" command
env MY_VAR="value" command
```

---

### 🧼 Unsetting Variables

```bash
unset MY_VAR
```

---

### 🧾 Common Environment Variables

| Variable   | Description                       |
| ---------- | --------------------------------- |
| `PATH`     | Directories searched for commands |
| `HOME`     | User’s home directory             |
| `USER`     | Current logged-in user            |
| `SHELL`    | Default shell (e.g., /bin/bash)   |
| `LANG`     | System language/locale            |
| `EDITOR`   | Default text editor               |
| `PWD`      | Current working directory         |
| `HOSTNAME` | System hostname                   |

---

### 🗂️ Persistent Environment Variables

To make variables persist across sessions and reboots, you can set them in specific files:

#### 🔹 User-Specific (e.g., Bash)

Add to `~/.bashrc`, `~/.bash_profile`, or `~/.profile`:

```bash
export MY_VAR="persistent value"
```

Then reload:

```bash
source ~/.bashrc
```

#### 🔹 System-Wide

Edit `/etc/environment` (no `export` needed):

```bash
MY_VAR="system value"
```

---

> 💡 Tip: Use uppercase names for environment variables by convention. Avoid spaces around `=` when assigning.

<br>

## ⚙️ Process Management in Linux

Processes are running instances of programs. Managing them is essential for monitoring system performance, terminating unresponsive tasks, and controlling resource usage.

---

When you view processes, you typically see a table with the following columns:

- **PID**: Process ID, a unique identifier for each process.
- **USER**: The user who owns the process.
- **%CPU**: Percentage of CPU usage by the process.
- **%MEM**: Percentage of memory usage by the process.
- **VSZ**: Virtual memory size of the process in kilobytes.
- **RSS**: Resident Set Size, the non-swapped physical memory used by the process.
- **TTY**: Type of Terminal - terminal associated with the process (if any). `pts` means pseudo-terminal, which is common for terminal sessions.
- **STAT**: Process state (e.g., S for sleeping, R for running, Sl for sleeping with threads, S+ for sleeping in the foreground, etc.).
- **START**: Time when the process started.
- **TIME**: Total CPU time each process consumed.
- **COMMAND**: The command that started the process, including any arguments.

### 📊 Viewing Running Processes

#### `ps` – Process Snapshot

- Shows currently running processes.
- Common usage:
  ```bash
  ps aux              # All processes with detailed info
  ps -ef              # Standard full-format listing
  ps -u username      # Processes by user
  ```

#### `top` – Real-Time Process Monitor

- Interactive view of live processes.
- Press `q` to quit, `k` to kill a process, `h` for help.
  ```bash
  top
  ```

#### `htop` – Enhanced `top` (if installed)

- Colorful, user-friendly interface.
- Use arrow keys to navigate, `F9` to kill.
  ```bash
  htop
  ```

#### `pidof` – Get PID of a Process

```bash
pidof firefox
```

#### `pgrep` – Search for Processes by Name

```bash
pgrep ssh
```

---

### 🛑 Controlling Processes

#### `kill` – Terminate a Process by PID

```bash
kill 1234            # Graceful termination (SIGTERM)
kill -9 1234         # Force kill (SIGKILL). -9 is a signal that forces termination without cleanup.
```

#### `killall` – Kill by Process Name

```bash
killall firefox
killall -9 vlc
```

#### `xkill` – Click to Kill a GUI Window (if installed)

```bash
xkill
```

---

### 🔁 Foreground & Background Jobs

#### Run in Background

```bash
sleep 60 &    # Run sleep command in the background (`&` at the end)
```

#### View Background Jobs

```bash
jobs
```

#### Bring Job to Foreground

```bash
fg %1
```

#### Send Job to Background

```bash
bg %1
```

---

### 🎚️ Process Priorities

#### `nice` – Start with Priority

- Range: `-20` (highest) to `19` (lowest)

```bash
nice -n 10 myscript.sh
```

#### `renice` – Change Priority of Running Process

```bash
renice -n 5 -p 1234
```

---

### 🌲 Visualizing Process Tree

#### `pstree` – Show Process Hierarchy

```bash
pstree
pstree -p            # Show PIDs
```

---

### 🧼 Miscellaneous

#### `nohup` – Run Process Immune to Hangups

```bash
nohup longtask.sh &
```

#### `watch` – Run a Command Repeatedly

```bash
watch -n 5 df -h
```

---

> 💡 Tip: Use `top` or `htop` to monitor system load, and `kill` or `renice` to manage misbehaving processes.

<br>

## 👥 User and Group Management in Linux

Linux is a multi-user system, and managing users and groups is essential for controlling access, organizing permissions, and maintaining security.

---

### 👤 User Management

#### `useradd` – Add a New User

```bash
sudo useradd username
```

- Common options:
  - `-m` : Create home directory
  - `-s /bin/bash` : Set default shell
  - `-c "Full Name"` : Add comment
  - `-g groupname` : Set primary group (each user belongs to one primary group - automatically created when new user is created, same name as the username)
  - `-G group1,group2` : Add to supplementary groups (each user can belong to zero or multiple supplementary groups)
  - `-u UID` : Set custom user ID
- Example:
  ```bash
  sudo useradd -m -s /bin/bash -c "Stjepan" stjepan
  ```

#### `passwd` – Set or Change User Password

```bash
sudo passwd username
```

#### `usermod` – Modify Existing User

```bash
sudo usermod -aG groupname username   # Add to group (a option appends user to the group without removing from other groups)
sudo usermod -s /bin/zsh username     # Change shell
sudo usermod -d /new/home username    # Change home directory
```

#### `userdel` – Delete a User

```bash
sudo userdel username
sudo userdel -r username   # Also remove home directory
```

---

### 👪 Group Management

#### `groupadd` – Create a New Group

```bash
sudo groupadd groupname
```

#### `groupdel` – Delete a Group

```bash
sudo groupdel groupname
```

#### `groupmod` – Modify Group Name or GID

```bash
sudo groupmod -n newname oldname
```

#### `gpasswd` – Manage Group Membership

```bash
sudo gpasswd -a username groupname   # Add user to group
sudo gpasswd -d username groupname   # Remove user from group
```

---

### 🔍 Viewing Users and Groups

#### `id` – Show User and Group IDs

```bash
id username
```

#### `groups` – Show Groups for a User (primary and supplementary)

```bash
groups username
```

#### `getent` – Query System Databases

```bash
getent passwd username
getent group groupname
```

#### `cat /etc/passwd` – List All Users

#### `cut -d: -f1 /etc/passwd` – List Usernames Only (extracts the first field, which is the username)

#### `cat /etc/group` – List All Groups

---

### 🧾 Switching between users

#### Switch to another user without password (if you have sudo privileges)

```bash
sudo su - username
```

#### Switch to another user with password

```bash
su - username
```

#### Run a single command as anther user without switching shell

```bash
sudo -u username command
```

#### Switch to root user

```bash
su -  # or
sudo su - # or
sudo -i
```

### 🧠 Special Notes

- **Primary group**: Set at user creation; shown in `/etc/passwd`.
- **Supplementary groups**: Additional groups a user belongs to.
- **System users**: Typically have UIDs < 1000 and no login shell.
- **Default files**:
  - `/etc/passwd` – User account info
  - `/etc/shadow` – Encrypted passwords
  - `/etc/group` – Group definitions
  - `/etc/gshadow` – Group passwords and admin info

---

> 💡 Tip: Use `sudo usermod -aG sudo username` to grant admin privileges on Ubuntu.

<br>

## 🔐 File Permissions and Ownership in Linux

Linux uses a permission and ownership model to control access to files and directories. Every file has an **owner**, a **group**, and a set of **permissions** for each.

---

### 👤 Ownership Basics

Each file or directory has:

- **User (u)** – The owner (creator by default)
- **Group (g)** – A group of users
- **Others (o)** – Everyone else

Use `ls -l` to view ownership and permissions:

```bash
ls -l filename
```

Example output:

```
-rw-r--r-- 1 stjepan devs 1234 Jul 4 10:00 report.txt
```

- Permissions: `rw-` for user (u), `r--` for group (g), `r--` for others (o). Note that the first character indicates the type (`-` for regular file, `d` for directory, `l` for symbolic link).
- `stjepan` is the owner
- `devs` is the group

---

### 🔑 Permission Types

| Symbol | Numeric | Meaning                |
| ------ | ------- | ---------------------- |
| `---`  | 0       | No permission          |
| `--x`  | 1       | Execute permission     |
| `-w-`  | 2       | Write permission       |
| `-wx`  | 3       | Write + Execute        |
| `r--`  | 4       | Read permission        |
| `r-x`  | 5       | Read + Execute         |
| `rw-`  | 6       | Read + Write           |
| `rwx`  | 7       | Read + Write + Execute |

Permissions are grouped as:

- **User** (owner)
- **Group**
- **Others**

Example: `-rwxr-xr--`

- User: `rwx` (7)
- Group: `r-x` (5)
- Others: `r--` (4)<br>
  --→ Octal: `chmod 754`

---

### 🛠️ Changing Permissions – `chmod`

#### 🔹 Symbolic Mode

```bash
chmod u+x file.txt     # Add execute for user
chmod g-w file.txt     # Remove write for group
chmod o=r file.txt     # Set read-only for others
chmod og-x *.txt       # Remove execute for group and others for all .txt files
chmod u=rwx,g=rx,o=r script.sh  # Set specific permissions
chmod a+x script.sh    # Add execute for all
```

#### 🔹 Numeric (Octal) Mode

```bash
chmod 755 script.sh    # rwxr-xr-x
chmod 644 file.txt     # rw-r--r--
chmod 700 secret.txt   # rwx------
```

---

### 👑 Changing Ownership – `chown` and `chgrp`

#### `chown` – Change File Owner

```bash
sudo chown newuser file.txt
sudo chown newuser:newgroup file.txt
```

#### `chgrp` – Change Group Only

```bash
sudo chgrp devs file.txt
```

#### Recursive Ownership Change

```bash
sudo chown -R stjepan:devs /project
```

---

### 🧪 Viewing Permissions and Ownership

- `ls -l` : Long listing with permissions
- `stat file.txt` : Detailed metadata
- `namei -l /path/to/file` : Show permissions along path

---

### 🧠 Special Permissions

| Name       | Symbol        | Description                            |
| ---------- | ------------- | -------------------------------------- |
| **SUID**   | `s` on user   | Run as file owner                      |
| **SGID**   | `s` on group  | Run as group or inherit group          |
| **Sticky** | `t` on others | Only owner can delete (used in `/tmp`) |

Examples:

```bash
chmod u+s script.sh     # Set SUID
chmod g+s shared_dir/   # Set SGID
chmod +t /tmp           # Set sticky bit
```

---

> 💡 Tip: Use `chmod -R`, `chown -R`, and `find ... -exec` for bulk permission changes. Always double-check before applying recursively!<br>
> Example:
>
> ```bash
> find /path/to/dir -type f -exec chmod 644 {} \;  # Change permissions of all files to 644, {} \; means execute the command on each file found
> find /path/to/dir -type d -exec chmod 755 {} \;  # Change permissions of all directories to 755
> ```
