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
