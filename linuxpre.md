## Terminal Prompt Breakdown

Example prompt:

```
root@localhost:~#
```

| Part        | Meaning                                           |
| ----------- | ------------------------------------------------- |
| `root`      | Current user                                      |
| `@`         | Separator                                         |
| `localhost` | Machine name (sometimes shows IP)                 |
| `:`         | Separator                                         |
| `~` (Tilde) | Current working directory → user's home directory |
| `#`         | Ready to type a command                           |

---

## 📂 Navigating the Filesystem

### **1️⃣ Show Files**

```
ls
```

### **2️⃣ Change Directory**

```
cd foldername
```

Special symbols:

* `.` → current directory
* `..` → parent directory
* `cd ..` → go one level up
* `cd ../..` → go two levels up
* `cd` or `cd ~` → go to home directory

### **3️⃣ Print Current Directory**

```
pwd
```

---

## 🗂️ Key Linux System Directories

| Directory | Purpose                          |
| --------- | -------------------------------- |
| `/`       | Root (top-level) directory       |
| `/bin`    | User executables                 |
| `/boot`   | System boot files                |
| `/dev`    | Hardware device info             |
| `/etc`    | System configuration files       |
| `/home`   | User home folders (except root)  |
| `/root`   | Home folder for **root** user    |
| `/lib`    | System libraries                 |
| `/media`  | Mount removable media (USB etc.) |
| `/mnt`    | Temporary mounted storage        |
| `/opt`    | Optional software                |
| `/sbin`   | System admin executables         |
| `/tmp`    | Temporary files                  |
| `/usr`    | User-installed software          |
| `/var`    | Log, database and runtime data   |

---

## 📁 Creating Directories & Files

### **Create Directory**

```
mkdir foldername
```

### **Create an Empty File**

```
touch file.txt
```

---

## ✏️ Read & Write Files

### **Read File Contents**

```
cat filename.txt
```

### **Write or Append to a File**

```
cat >> filename.txt
```

Then type content → press **Ctrl + D** to save and exit.

---

## 🔁 Useful Shortcuts

* **Tab** → auto-complete file/folder names
* **Up/Down arrows** → cycle through command history

---

## 🔧 File Operations

### **Move/Rename File**

```
mv source target
```

Example:

```
mv file.txt folder/
```

### **Copy File**

```
cp file.txt copy.txt
```

---

## 🗑️ Delete Files & Folders

### **Delete File**

```
rm file.txt
```

#### Delete with confirmation:

```
rm -i file.txt
```

### **Delete Empty Directory**

```
rm -d foldername
```

(or)

```
rmdir foldername
```

### **Delete Directory with Contents (Recursive)**

⚠️ **Dangerous Command**

```
rm -r foldername
```

### **Pattern Delete**

```
rm *.txt
```

Deletes all `.txt` files.

---

## 🔎 Finding Files

### **Search in Current Directory**

```
find . -name filename
```

### **Case-Insensitive Search**

```
find . -iname filename
```

### **Search By Pattern**

```
find . -name "*.txt"
```

### **Search Only Directories**

```
find . -type d -name foldername
```

---

## 🧹 Useful Cleanup Command

```
clear
```

---

## 👤 What is a User?

* A user is an identity used to log into a Linux system.
* The **root user** is the **superuser** with full permissions over the system.

> Only root or users with elevated permissions can manage users/groups.

---

## 👥 What are Groups?

* A **group** is a collection of users.
* Groups allow permission management across multiple users efficiently.

### Types of Groups:

| Type                 | Description                                                                     |
| -------------------- | ------------------------------------------------------------------------------- |
| **Primary Group**    | Default group assigned to a user; every created file belongs to this group.     |
| **Secondary Groups** | Optional groups a user can join (up to 15); used for shared access permissions. |

Example: Developers accessing Docker tools may be in the `docker` group.

---

## 🛠️ User Management Commands

### ✔️ Add a User

```
adduser username
```

Prompts for password & optional details.

### ✔️ View All Users

```
cat /etc/passwd
```

### ✔️ Login as Another User

(After logout)

```
login username
```

### ✔️ Delete a User

```
deluser username
```

### ✔️ Change a User Password

```
passwd username
```

---

## 🧑‍🤝‍🧑 Group Management Commands

### ✔️ Create a Group

```
addgroup groupname
```

### ✔️ Add a User to a Group

```
usermod -a -G groupname username
```

(`-a` = append, `-G` = group)

### ✔️ Check Group Membership

```
groups username
```

### ✔️ Remove User from Group

```
gpasswd -d username groupname
```

### ✔️ Delete a Group

```
delgroup groupname
```

---

## 🔐 Granting Permissions Using `sudoers` File

* File used to define which users/groups can run privileged commands.
* Edit using safe method:

```
visudo
```

### Example: Give a Specific User Command Permission

```
username ALL=(ALL) /usr/bin/top
```

### Example: Give Group Command Permissions

```
%groupname ALL=(ALL) /usr/bin/ls, /usr/bin/less, /usr/bin/apt
```

---

## 🔍 Finding Command Path

Use **which** to locate the executable path (needed for sudoers):

```
which commandname
```

Example:

```
which cat
→ /usr/bin/cat
```

---

## 📝 Useful Notes

* Regular users cannot access files belonging to other users unless given permission.
* The root user can access anything without restrictions.
* Linux allows multiple users logged in simultaneously — helpful for servers.

---

## 🚪 Logging Out

```
logout
```

---

##  Common Editors Mentioned

* `nano` (simpler editor opened during visudo)
* `vim` (used when viewing some files; exit with `Esc → :wq`)

---

## 🔑 Understanding File Permissions in Linux

Linux assigns permissions to:

| Level          | Meaning                     |
| -------------- | --------------------------- |
| **User (u)**   | Owner of the file           |
| **Group (g)**  | Members of the file's group |
| **Others (o)** | All other users             |
| **All (a)**    | All three above (u+g+o)     |

---

## 📂 Viewing File Permissions

Use:

```
ls -l
```

Example output format:

```
drwxr-xr-x 2 root root 4096 Jan 10 08:50 myfolder
```

Breakdown:

| Section    | Meaning                                       |
| ---------- | --------------------------------------------- |
| `d`        | File type (directory `d`, file `-`, link `l`) |
| `rwx`      | User permissions → read, write, execute       |
| `r-x`      | Group permissions                             |
| `r-x`      | Other users' permissions                      |
| `2`        | Number of items inside (if directory)         |
| `root`     | Owner                                         |
| `root`     | Group                                         |
| `4096`     | Size                                          |
| `myfolder` | File/folder name                              |

---

## 🧠 Permission Symbols

| Symbol | Meaning       |
| ------ | ------------- |
| `r`    | Read          |
| `w`    | Write         |
| `x`    | Execute       |
| `-`    | No permission |

Sample interpretation:

```
-rw-r--r--
```

Meaning:

* User: read + write
* Group: read
* Others: read

---

## 🛠 Using `chmod` (Change Mode)

Format:

```
chmod <WHO><ACTION><PERMISSION> file
```

Where:

| WHO | Meaning |
| --- | ------- |
| `u` | user    |
| `g` | group   |
| `o` | others  |
| `a` | all     |

| ACTION | Meaning                                      |
| ------ | -------------------------------------------- |
| `+`    | add permission                               |
| `-`    | remove permission                            |
| `=`    | set permission exactly (overwrites previous) |

| PERMISSIONS | Meaning |
| ----------- | ------- |
| `r`         | read    |
| `w`         | write   |
| `x`         | execute |

---

## 📌 Examples

### ➕ Add Write Permission for Everyone

```
chmod a+w file.txt
```

### ➕ Add Execute Permission for User Only

```
chmod u+x script.sh
```

### ➖ Remove Write Permission from Others

```
chmod o-w notes.txt
```

### 🟦 Set Exact Permissions (overwrites previous)

```
chmod a=rw file.txt
```

(Everyone can read/write but not execute)

---

## 📁 Changing Permissions for Directories

To apply changes **recursively** to all files inside a directory:

```
chmod -R a+w foldername
```

`-R` → recursive.

---

## 🔁 Multiple Permission Updates at Once

```
chmod g+w,o-r,a+x file.txt
```

---

## 📍 Default Behavior

If WHO is **not specified**, Linux assumes **all (a)**.

Example:

```
chmod +x file.sh
```

→ Adds execute permission for user, group, and others.

---

## 🧪 Quick Check

After modifying permissions, run:

```
ls -l
```

to verify changes.

---

## 🧠 What is a Process?

A **process** is a running program or task on a Linux system.
Examples: scripts, services, servers, background tasks, etc.

Understanding processes is essential when:

* Debugging a frozen or crashed program
* Managing server applications
* Terminating unwanted or looping programs

---

## 🔍 Viewing Running Processes

### **1️⃣ `top` Command**

```
top
```

Displays a live, updating process monitor.

Shows:

* System uptime
* Logged-in users
* CPU and memory usage
* Active task count
* Running processes with:

  * PID (Process ID)
  * User
  * CPU %
  * Memory %
  * Command name

Navigation:

| Key        | Action |
| ---------- | ------ |
| ↑ / ↓      | Scroll |
| `Ctrl + C` | Exit   |

---

### **2️⃣ Installing & Using `htop` (Improved Top)**

#### Install:

```
apt install htop
```

(`apt` = Advanced Package Tool for installing/removing/updating software)

#### Run:

```
htop
```

Features of `htop`:

* Color-coded display
* CPU/RAM visualization
* Sorting options
* Search/filter functionality
* Built-in kill options

Useful keys:

| Key        | Purpose               |
| ---------- | --------------------- |
| `F3`       | Search for process    |
| `F9`       | Kill selected process |
| `Ctrl + C` | Exit                  |

---

## 🧨 Killing Processes

Processes are terminated by sending **signals**.

Common signals:

| Signal    | Number | Meaning                        |
| --------- | ------ | ------------------------------ |
| `SIGTERM` | 15     | Ask process to stop gracefully |
| `SIGKILL` | 9      | Force kill (no cleanup)        |

### **Methods:**

#### 🟥 Kill by PID

```
kill <PID>
```

Example:

```
kill 1234
```

#### 🟥 Kill by Name

```
killall processname
```

Example:

```
killall python
```

---

## 📃 Listing Process Information

### **`ps` — Process Status**

```
ps
```

Shows processes owned by the current terminal session.

To get full process info:

```
ps aux
```

Explanation of flags:

| Flag | Meaning                          |
| ---- | -------------------------------- |
| `a`  | Show processes from all users    |
| `u`  | Show process owner/user          |
| `x`  | Show background/system processes |

Example:

```
ps aux
```

---

## 📌 Summary of Commands

| Purpose                         | Command                 |
| ------------------------------- | ----------------------- |
| View basic processes            | `ps`                    |
| View all processes (systemwide) | `ps aux`                |
| Live monitoring                 | `top`                   |
| Improved monitoring tool        | `htop`                  |
| Kill using PID                  | `kill <pid>`            |
| Kill all with name              | `killall <name>`        |
| Kill with forced shutdown       | `kill -9 <pid>`         |
| Install packages (like htop)    | `apt install <package>` |

---


## 🖧 1️⃣ `ifconfig`

```
ifconfig
```

Shows detailed information about **network interfaces**, including:

* Interface names (e.g., `eth0`, `lo`)
* IPv4 & IPv6 addresses (`inet`, `inet6`)
* MAC address (`ether`)
* Basic packet statistics

Terminology:

| Interface | Meaning                                    |
| --------- | ------------------------------------------ |
| `eth0`    | Physical Ethernet network adapter          |
| `lo`      | Loopback (local virtual network interface) |

---

## 📡 2️⃣ `ip` Command (Modern Alternative to ifconfig)

### Show IPv4 address:

```
ip -4 addr
```

### Show IPv6 address:

```
ip -6 addr
```

Look for `inet` (IPv4) or `inet6` (IPv6).

---

## 🧪 3️⃣ `netstat`

Shows **network connections, listening ports, and routing info**.

```
netstat
```

Useful flags:

| Command      | Meaning                          |
| ------------ | -------------------------------- |
| `netstat -a` | Show all listening TCP/UDP ports |
| `netstat -t` | Show only TCP connections        |
| `netstat -u` | Show only UDP connections        |
| `netstat -l` | Show only active listening ports |

---

## 🛠️ 4️⃣ `curl`

Used to send HTTP requests and inspect responses from endpoints.

### Basic GET request:

```
curl https://example.com
```

### Send POST request:

```
curl -X POST https://example.com
```

### Send request with body data:

```
curl -X POST --data "p1=value1&p2=value2" https://example.com
```

or

```
curl -X POST -d "p1=value1" -d "p2=value2" https://example.com
```

### Save response to file:

```
curl -o result.html https://example.com
```

### View only headers:

```
curl -I https://example.com
```

### Add custom header (e.g., JSON content-type):

```
curl --header "Content-Type: application/json" https://example.com
```

---

## 📍 5️⃣ `ping`

Used to test if a host is reachable.

```
ping google.com
```

* Continues until stopped
* Shows latency and packet statistics

Stop ping:

```
Ctrl + C
```

---

## 🧰 Extra Reference

| Command        | Purpose                              |
| -------------- | ------------------------------------ |
| `ifconfig`     | View network interface configuration |
| `ip -4/6 addr` | Quick IPv4/IPv6 lookup               |
| `netstat`      | View ports, connections, protocols   |
| `curl`         | Send HTTP requests                   |
| `ping`         | Check server/network connectivity    |

---


## 🔑 Concept: Public & Private Key Authentication

* You generate **two keys**:

  * **Private key** → stays on your local machine
  * **Public key** → stored on the server (`~/.ssh/authorized_keys`)
* When you SSH:

  * Your client sends proof of your private key
  * Server checks if it matches the public key
  * If they match → you’re authenticated
* More secure than just a password (harder to brute-force).
* Typically:

  * You **enable key auth**
  * Then optionally **disable password login** for extra security.

---

## 🍏 On macOS: Generating & Using SSH Keys

### 1️⃣ Check if keys already exist (important!)

```bash
ls ~/.ssh/id_rsa*
```

* If you see:

  * `id_rsa` (private key)
  * `id_rsa.pub` (public key)
    → **Do NOT overwrite them** unless you know what you’re doing.

---

### 2️⃣ Generate a new key pair (if none exists)

```bash
ssh-keygen -b 4096
```

* `-b 4096` → 4096-bit key (more secure than 2048).
* When asked for:

  * **File to save** → press Enter to accept default (`~/.ssh/id_rsa`)

    * Or type a different name if you want a separate key.
  * **Passphrase** → optional but recommended (decrypts your private key when used).

---

### 3️⃣ Copy public key to server

```bash
ssh-copy-id root@<server-ip>
```

* Replace `<server-ip>` with your server's IP.
* Enter the **user's password** (e.g., root) one last time.
* This adds your public key to: `~/.ssh/authorized_keys` on the server.

---

### 4️⃣ Login using the key

```bash
ssh root@<server-ip>
```

* Now it should:

  * Ask for your **key passphrase** (not server password)
  * Then log you in.

> Usually you do this for a **non-root** user with sudo privileges, not root itself.

---

## 🪟 On Windows: Using PuTTY & PuTTYgen

### 1️⃣ Generate keys with PuTTYgen

* Download & open **PuTTYgen**.
* Select:

  * Type: **RSA**
  * Bits: **4096**
* Click **Generate** and move your mouse randomly over the blank area to create entropy.
* Set a **Key passphrase** (e.g., `1 2 3 4 5`, but use something strong in real life).

Save keys:

* **Save public key** → as `.txt` (e.g., `public-key.txt`)
* **Save private key** → as `.ppk` (e.g., `private-key.ppk`)

---

### 2️⃣ Configure PuTTY to use the private key

1. Open **PuTTY**.
2. Load your saved session (e.g., `tutorial`) or enter:

   * Host: `<server-ip>`
   * Port: `22`
   * Connection type: SSH
3. Go to:

   * `Connection → SSH → Auth`
   * Browse and select your `private-key.ppk`
4. Go back to **Session** → click **Save** to keep settings.

---

### 3️⃣ Prepare server’s `~/.ssh/authorized_keys`

Login normally (password-based) first:

* Open PuTTY session
* Login as: `root` (or other user)
* Enter password

Then on the **server**:

#### a) Check for `.ssh` directory

```bash
cd ~
cd .ssh
```

* If it **fails** (no such file), create it:

  ```bash
  mkdir .ssh
  cd .ssh
  touch authorized_keys
  ```

#### b) Set proper permissions

From `~`:

```bash
chmod a+rwx,g-rwx,o-rwx .ssh
cd .ssh
chmod a+rwx,u-x,g-rwx,o-rwx authorized_keys
```

(Exact perms are a bit specific; key idea: `.ssh` and `authorized_keys` must be restricted.)

---

### 4️⃣ Add the public key to `authorized_keys`

* Open the **public-key.txt** on Windows.
* Copy **only** the key content (no comments, no surrounding text).
* In PuTTY, open the file on server:

  ```bash
  nano authorized_keys
  ```
* Paste with **right-click**.
* Ensure the line starts with:

  ```text
  ssh-rsa <your-public-key>
  ```

  and is **all on one line**.
* Save in nano:

  * `Ctrl + S`
  * `Ctrl + X`

---

### 5️⃣ Reconnect using public/private key

* Close PuTTY.
* Open your saved session again.
* Login as the same user (e.g., `root`).
* It should:

  * Say **"Authenticating with public key"**
  * Ask for your **key passphrase**
  * Log you in without server password

---

## 📂 FTP / SFTP with FileZilla

### 🔁 Goal

Transfer files **to/from your server** using GUI instead of command-line SCP.

FileZilla supports **SFTP (over SSH)**.

---

### 1️⃣ Connect with username/password (simple way)

1. Open **FileZilla**.
2. At the top (Quickconnect bar):

   * Host: `<server-ip>`
   * Username: `root` (or other user)
   * Password: user’s SSH password
   * Port: `22`
3. Click **Quickconnect**.

Left side = your **local machine**
Right side = **server filesystem**

You can:

* Drag files from local → server
* Drag from server → local
* Delete or rename files on server (according to permissions)

---

### 2️⃣ Use SSH key with FileZilla (no password)

1. In FileZilla, go to:

   * `Edit → Settings → SFTP`
2. Click **Add key file…**
3. Select your **private key file**:

   * On Windows: `.ppk` (PuTTY private key)
   * On Mac/Linux: usually `~/.ssh/id_rsa`
4. Click **OK** to save.

Now connect:

* Host: `<server-ip>`
* Username: `root` (or other user)
* Port: `22`
* No password needed → It will:

  * Ask for **key passphrase**
  * Authenticate using your key

---

## ✅ After This Video, You Should Understand:

* What public/private key authentication is
* How to:

  * Generate SSH keys on Mac (`ssh-keygen`) and Windows (PuTTYgen)
  * Add your public key to `~/.ssh/authorized_keys`
  * Login via SSH using keys (no password)
* How to:

  * Use FileZilla to connect to your server
  * Upload/download files over SFTP
  * Use your SSH key in FileZilla

---

## 📄 View Environment Variables

### Show all current environment variables:

```bash
env
```

### Show the value of a specific variable:

```bash
printenv VARIABLE_NAME
```

Example:

```bash
printenv HOME
```

---

## 💡 Access Variables (Inside Shell or Scripts)

Use `$` prefix:

```bash
echo $USER
echo $PATH
```

---

## 🏗 Create Temporary Environment Variables (Session Only)

Temporary means: **valid only until terminal closes.**

### Create:

```bash
export VAR_NAME="value"
```

### Example:

```bash
export API_KEY="123abc"
```

### Verify:

```bash
printenv API_KEY
```

When you close the terminal → **this disappears.**

---

## 🔁 Modify an Existing Variable

Just re-export:

```bash
export VAR_NAME="new_value"
```

---

## 🗑 Remove an Environment Variable (Temporary Only)

```bash
unset VAR_NAME
```

Example:

```bash
unset API_KEY
```

---

## ♻️ Persistent Environment Variables

Persistent variables survive terminal restart and session changes.

### **1️⃣ User-Level Environment Variables**

Stored in:

```
~/.bashrc
```

Edit the file:

```bash
nano ~/.bashrc
```

Add:

```bash
export PROJECT_MODE="development"
```

Save, then reload:

```bash
source ~/.bashrc
```

Now `PROJECT_MODE` exists every time **that user** logs in.

---

### **2️⃣ System-Wide (Global) Environment Variables**

Stored in:

```
/etc/environment
```

Edit:

```bash
sudo nano /etc/environment
```

Add variables:

```
API_SERVER="https://example.com"
GLOBAL_MODE="production"
```

Save and **reload environment**:

```bash
source /etc/environment
```

These apply to **all users on the system.**

---

## 🧪 Useful Built-In Variables

| Variable | Meaning                           |
| -------- | --------------------------------- |
| `HOME`   | User’s home directory             |
| `PATH`   | Where shell looks for executables |
| `USER`   | Logged-in username                |
| `PWD`    | Current directory                 |
| `SHELL`  | Which shell you're using          |

Example:

```bash
echo $PATH
```

---

## 🧠 Summary Table

| Action                  | Command                                         |
| ----------------------- | ----------------------------------------------- |
| Show all variables      | `env`                                           |
| Show one variable       | `printenv NAME`                                 |
| Create temporary        | `export NAME=value`                             |
| Remove temporary        | `unset NAME`                                    |
| Create permanent (user) | Add `export NAME=value` → `~/.bashrc`           |
| Create global           | Add `NAME=value` → `/etc/environment`           |
| Apply changes           | `source ~/.bashrc` or `source /etc/environment` |

---

## **Linux Text Editors – Understanding Nano, Vim, and Emacs**

In Linux, text editors are essential tools used for editing configuration files, scripts, application settings, and general text while working through the terminal. The video mainly focuses on three editors: **Nano**, **Vim**, and **Emacs**, with Nano and Vim being the default editors included with most Linux distributions like Ubuntu.

---

### **Nano**

Nano is considered the simplest editor and is often preferred by beginners because of its intuitive design. You open nano by typing the command followed by a filename, for example:

```
nano test.txt
```

If the file already exists, Nano opens it; otherwise, it creates it.

Inside Nano, you can type normally. There is no "mode" system like Vim. At the bottom of the screen, Nano displays shortcuts — the caret symbol `^` represents the **Ctrl** key. For example, `^X` means pressing **Ctrl + X**.

To save changes, you use **Ctrl + S**, and to save with a new file name (Save As), you use **Ctrl + O**. Exiting Nano requires **Ctrl + X**, and if there are unsaved changes, Nano will prompt you to save before closing.

Copying and pasting works differently than on typical GUI editors. You must first mark text using **Ctrl + ^** (Control + 6). Once a region is highlighted, you copy it with **Alt + 6**, paste with **Ctrl + U**, and cut using **Ctrl + K**. While not the most user-friendly approach, Nano remains very practical for quick edits because the shortcuts are shown on screen and easy to reference.

---

### **Vim**

Vim is a more advanced editor with a steep learning curve. It is much more powerful than Nano and highly customizable, making it popular among experienced programmers and system administrators. Vim works using different modes, mainly:

* **Command Mode** — used for navigation, editing commands, and issuing instructions.
* **Insert Mode** — used for typing text into the file.

When you first open Vim using:

```
vim test2.txt
```

You begin in command mode. Pressing keys won’t type text — instead, Vim interprets them as commands. To begin typing, you must enter insert mode by pressing **i**, and typing the **Esc** key exits back to command mode.

One of the most confusing parts of Vim is exiting. To save changes and exit, you type the following while in command mode:

```
:wq
```

To exit without saving:

```
:q!
```

Editing and navigation in Vim are powerful. Visual mode is activated with `v`, allowing you to highlight text. Copying is referred to as *yanking* and is done with the key `y`, and pasting with `p`. `u` performs undo, while `Ctrl + r` redoes an action. Several shortcuts exist for movement, such as `w` to jump one word forward, `b` to jump backward, `0` to move to the beginning of a line, and `$` to move to the end.

Vim is highly efficient once learned, especially for users who frequently edit files remotely through SSH.

---

### **Emacs**

Emacs is another powerful editor available on Linux. It is not installed by default, so you need to install it first using:

```
sudo apt-get install emacs
```

Once installed, you start it by simply typing `emacs`.

Emacs is known for being feature-rich and extendable, often considered closer to a full development environment than just an editor. In this video, Emacs is mentioned briefly, and the only action demonstrated is how to exit using **Ctrl + Z**. (Though the proper exit shortcut is usually `Ctrl + X` followed by `Ctrl + C`.)

---

### **sudo Explanation**

The video briefly introduces the `sudo` command. `sudo` stands for **"super user do"** and allows a non-root user to run commands with administrator privileges. This is required when installing software or modifying protected system files. Only users in the **sudo group** can execute sudo commands.

---

Here are **proper notes** (not formatted as a cheat sheet) summarizing and explaining the content of the video in a clear flow:

---

## **Notes: The `grep` Command in Linux**

The `grep` command is one of the most commonly used tools in Linux. Its main purpose is to **search for text patterns** within files or program output. `grep` exists on practically all Linux distributions and is especially valuable for developers because it helps locate specific information inside large files, logs, source code, or anything with text content.

At its core, the basic structure of a `grep` command looks like this:

```
grep pattern filename
```

Here, **pattern** represents what you are trying to search for, and **filename** is the file where the search will occur.

---

### **How basic grep works**

When you run a simple grep command like:

```
grep a grep.txt
```

`grep` scans the entire file and prints the **entire line** for every match it finds. Even if only one character matches the pattern, the whole line is included in the output. Matches in the output are often shown in a highlighted color (typically red).

This behavior shows that `grep` works line-by-line and searches for patterns rather than just fixed words.

---

### **Grep and Regular Expressions**

`grep` does not just search literal text — it accepts **regular expressions**, which means you can search using flexible patterns.

Some examples shown in the video:

* A **dot (`.`)** in a pattern means “match any single character.”

  Example:

  ```
  grep "a." grep.txt
  ```

  This finds anything that starts with `a` and has any other character following it.

* If the pattern becomes `"a.a"`, then the command looks for anything where an `a` is followed by any character and then another `a`.

Another feature demonstrated is using square brackets `[]`, which let you match one character from a list:

```
grep "[a,b].a" grep.txt
```

This matches patterns beginning with `a` or `b`, followed by any character, and ending in `a`.

Regular expressions can be expanded a lot further, but the video focuses on basic usage.

---

### **Pattern alternation (OR behavior)**

`grep` can search for multiple patterns using the OR operator:

```
grep "pattern1\|pattern2" file
```

This shows results matching either expression.

---

### **Matching the beginning and end of lines**

Two special symbols are used for positional matching:

* `^` (shift + 6) — matches the **start** of a line
* `$` — matches the **end** of a line

Examples:

```
grep "^ab" grep.txt   # pattern at start of line
grep "ab$" grep.txt   # pattern at end of line
```

---

### **Using grep with Options**

Several flags modify grep's behavior:

* `-n` — shows line numbers where matches occur
* `-o` — outputs only the matching part of the line instead of the whole line
* `-C number` — shows context around the match (the specified number of lines before and after)

An example from the video:

```
grep -C 4 "pattern" file.txt
```

This is useful for reading matches in context, especially in code.

---

### **Filtering Command Output with Grep**

One of the most useful aspects of grep is its ability to filter the output of other commands using a **pipe (`|`)**.

Example:

```
ls | grep txt
```

This returns only filenames that include `txt`. You can use the same patterns and regular expressions here just like with files.

---

### **Recursive searching (search through directories)**

`grep` can search through multiple files automatically using the `-r` or `--recursive` flag.

Example:

```
grep -r "hello" .
```

This searches for the word "hello" in all files within the current directory and its subdirectories.

---

### **Practical Uses**

`grep` can be applied in various situations such as:

* Searching log files for errors
* Locating specific text inside code repositories
* Filtering and analyzing command output
* Finding configuration values across multiple files

Because of its flexibility and speed, it's considered an essential Linux command — especially when combined with pipes and regular expressions.

---


## **Notes: Shell Scripts in Linux**

Shell scripts allow you to automate terminal commands by placing them inside a file and running it like a program. Instead of typing one command at a time in the terminal, a shell script runs multiple commands sequentially, and it can also include programming logic such as variables, functions, conditionals, and loops.

A shell script behaves similarly to scripts in languages like Python or JavaScript, except the syntax follows the shell language (Bash in this case).

---

### **Creating a Shell Script**

A shell script is simply a text file ending with the `.sh` extension. For example:

```
test.sh
```

To create the file, you can use a text editor such as Nano:

```
nano test.sh
```

---

### **The Shebang Line**

At the top of the script, a special line is required. This is called a **shebang** and tells the system which shell interpreter should run the script:

```
#!/bin/bash
```

This line must appear at the very beginning of the file.

---

### **Writing Commands**

Anything you can type in the terminal can also be placed inside the script. For example:

```
echo "Hello World!"
echo "Hi"
```

To run the script, you need execute permission. You add it using:

```
chmod +x test.sh
```

Then run it like this:

```
./test.sh
```

If you forget execute permissions, you will get a "Permission denied" message.

---

### **Functions in Shell Scripts**

You can define reusable blocks of code using functions. A function is defined using this format:

```
function_name() {
    echo "Called function"
}
```

To execute the function, you simply write its name:

```
function_name
```

---

### **Passing Arguments to Functions**

Functions can accept arguments. Arguments inside a script (or function) are accessed using numbered variables:

* `$1` → first argument
* `$2` → second argument
* and so on...

Example:

```
print() {
    echo "Value: $1"
}

print "Hello"
```

Multiple arguments are passed separated by spaces, unless wrapped in quotes.

---

### **Variables in Shell Scripts**

Variables are assigned using `=` without spaces:

```
x=6
```

To use the value stored in a variable, prefix its name with `$`:

```
echo $x
```

Variable naming follows typical programming conventions: no spaces, no special characters (besides `_`), and preferably not starting with a number.

---

### **Exit Codes**

When a script finishes running, it returns an exit code. By default, successful execution returns `0`.

You can explicitly exit a script using:

```
exit 0   # success
exit 1   # indicates an error or failure
```

Once `exit` is executed, the script stops immediately.

Exit codes are useful when scripts are used by other programs or automation tools.

---

### **Conditional Logic (if / elif / else)**

Shell scripts allow conditional execution. The basic structure looks like this:

```
if [[ $1 == "Tim" ]]; then
    echo "Tim is great"
elif [[ $1 == "Joe" ]]; then
    echo "Joe"
else
    echo "Not Tim"
fi
```

Important rules:

* Conditions go inside `[[ ]]`
* Each block ends with `fi`
* `elif` works as "else if"

---

### **Reading User Input**

You can request input while the script is running using the `read` command:

```
read -p "Name: " text
```

The `-p` flag displays a prompt before input.

Whatever the user types is stored in the variable (`text` in this example). You can then use it:

```
echo $text
```

---
Here are clear, normal notes (not in cheat-sheet style) summarizing the content of the Cron Jobs video:

---

## **Notes: Cron Jobs and Crontab in Linux**

Cron is a built-in Linux scheduling system that allows commands, scripts, or tasks to run automatically at specific intervals. These scheduled tasks are called **cron jobs**. They are useful for repetitive automation such as backups, cleanup tasks, sending reports, running maintenance scripts, or performing system operations at fixed times.

Examples of typical automations include:

* Running a backup every night
* Sending a status report weekly
* Cleaning temporary files every month
* Executing a script at system reboot

Cron runs continuously in the background, checking its schedule and executing jobs when their trigger time is reached.

---

### **Crontab (Cron Table)**

The **crontab** is the configuration file where cron jobs are stored. Each user on the system has their own separate crontab file, meaning scheduled tasks are user-specific.

To display the current crontab for the user:

```
crontab -l
```

If no crontab exists yet for the user, the output may state that one does not exist.

To create or edit the crontab:

```
crontab -e
```

The first time this is run, Linux may ask which editor you want to use (Nano, Vim, etc.). The file that opens is where cron jobs are defined.

---

### **How Cron Job Syntax Works**

A cron job line has this format:

```
<time schedule>   <command to execute>
```

The time schedule portion consists of **five fields**, each separated by spaces:

| Field | Meaning                                  |
| ----- | ---------------------------------------- |
| 1st   | Minutes (0–59)                           |
| 2nd   | Hour (0–23)                              |
| 3rd   | Day of month (1–31)                      |
| 4th   | Month (1–12)                             |
| 5th   | Day of week (0–6, where 0 or 7 = Sunday) |

Example layout:

```
*  *  *  *  *   /path/to/command
```

The asterisk (`*`) means "any" or "match all." Cron then uses these for scheduling patterns.

---

### **Examples of Time Patterns**

* **Every minute:**

  ```
  * * * * * command
  ```

* **Every 5 minutes:**

  ```
  */5 * * * * command
  ```

* **At minute 15 of every hour:**

  ```
  15 * * * * command
  ```

* **At 5:00 AM every day:**

  ```
  0 5 * * * command
  ```

* **On the first day of every month at 10:30 AM:**

  ```
  30 10 1 * * command
  ```

* **Every Monday to Friday at midnight:**

  ```
  0 0 * * 1-5 command
  ```

Ranges (e.g., `1-5`) and lists (e.g., `5,10,15`) are also supported, allowing multiple runtime schedules in one line.

---

### **Running Custom Scripts in Cron**

You can instruct cron to execute a script by providing the script’s **full file path**. Example:

```
*/1 * * * * /root/test.sh
```

This runs `test.sh` every minute.

The script must:

* Have the executable bit set (`chmod +x file.sh`)
* Include a valid shebang (`#!/bin/bash`)

---

### **Special Cron Keywords**

Cron provides shortcut identifiers to avoid manually writing time fields.

Examples include:

| Shortcut                 | Meaning                      |
| ------------------------ | ---------------------------- |
| `@reboot`                | Run once every boot          |
| `@daily` or `@midnight`  | Run once per day at midnight |
| `@hourly`                | Run once every hour          |
| `@weekly`                | Run once per week            |
| `@monthly`               | Run once per month           |
| `@annually` or `@yearly` | Run once per year            |

Example usage:

```
@daily /root/backup.sh
@reboot /root/startup_script.sh
```

These are convenient when exact timing values are not required.

---
Here are the **notes** for this video in the same format as before — clear, structured, and written like learning material (not a cheat sheet):

---

## **Notes: Additional Linux Commands (Intermediate Level)**

As you continue working in Linux beyond basic navigation and file management, you’ll encounter additional commands that aren’t used constantly but become very useful in certain situations. These commands help with system monitoring, communication, documentation lookup, and file output manipulation. This video explores a set of such commands to expand your Linux toolset.

---

### **1. `uptime`**

The `uptime` command displays how long the system has been running, the current number of logged-in users, and the system load averages. This gives a quick snapshot of system health and usage.

Example output includes:

* Time since the last reboot
* Number of users currently logged in
* Load averages for the last 1, 5, and 15 minutes

---

### **2. User Communication Commands**

Linux allows multiple users to be logged in simultaneously. Some commands let users send messages to each other directly via the terminal.

#### **`wall` (write all)**

This command broadcasts a message to **all logged-in users**.

Example:

```
wall "Hello"
```

All users currently logged in will see the broadcast message, including the sender.

#### **`write username`**

If communication is intended for **one specific user**, the `write` command is used:

```
write tim
```

This opens a session where whatever you type is delivered live to that user's terminal. To finish writing and return to your own shell, press `CTRL + D` (EOF).

#### **Message Permission Control: `mesg`**

Users can control whether they want to allow incoming message sessions. This is done with:

* `mesg y` → allow others to message you
* `mesg n` → block incoming messages

If messages are blocked, attempts to use `write` will notify the sender:
**"user has messages disabled."**

---

### **3. `who`**

The `who` command shows which users are currently logged into the system. This pairs well with messaging commands.

---

### **4. `free`**

This command displays memory usage information, including:

* Total system memory
* Amount used
* Free memory
* Swap usage

This is especially helpful when monitoring heavy workloads such as machine learning scripts or server applications.

---

### **5. `sort`**

This command helps organize text inside files alphabetically.

Usage:

```
sort file.txt
```

* By default, sorting is done in **ascending order**.
* Adding `-r` sorts in **reverse order**.

Sorting is useful for analyzing output or working with structured logs or lists.

---

### **6. Shutdown and Reboot from Terminal**

Linux supports shutting down or rebooting directly via command-line instructions.

Examples:

* Shutdown immediately:

  ```
  shutdown -h now
  ```

* Shutdown in 10 minutes:

  ```
  shutdown -h +10
  ```

* Restart immediately:

  ```
  shutdown -r now
  ```

These commands are especially relevant when managing remote servers or automating shutdown scripts.

---

### **7. Manuals and Command Documentation**

Linux includes built-in documentation and inspection tools.

#### **`man` (manual)**

Displays a detailed manual page for a command, explaining:

* What it does
* Available options
* Syntax

Example:

```
man ls
```

Exit with `q`.

#### **`whatis`**

If you need only a short summary instead of a full manual, you can ask:

```
whatis sort
```

This gives a one-line description of the command.

---

### **8. `tail`**

This command prints the last lines of a file. By default, it shows the last **10 lines**.

```
tail file.txt
```

You can customize how many lines appear using `-n`:

```
tail -n 25 file.txt
```

This command is commonly used when monitoring logs or watching recent updates to files.

---
Here are your **clear formatted notes** (not a cheat sheet, not bullets-only — full explanation style), summarizing the video step-by-step with reasoning so it’s easy to revisit later:

---

## **Notes: Hosting a Flask Web Application on a Linux Server**

In this lesson, the goal is to deploy a simple Flask web application directly on a remote Linux machine so that it becomes accessible publicly through its IP address. This process demonstrates how real applications are hosted using Python, a web server, and reverse proxy routing. The example uses Flask, NGINX, and Gunicorn — a very common production setup.

---

### **1. Preparing the Server: Python & PIP Installation**

Most Linux servers come with Python installed, but specifically **Python 3** is needed because modern Flask and dependencies do not support Python 2.

Commands used:

```
apt-get install python3
apt-get install python3-pip
```

PIP is required because Flask will be installed through it.
(If the user is *not* root, they must prefix commands with `sudo`.)

---

### **2. Creating the Flask Project**

Inside the user's home directory, a folder is created to store the Flask application:

```
mkdir flask_project
cd flask_project
```

Inside that folder, a special Python file is created named:

```
__init__.py
```

This name is important because Gunicorn expects this structure when treating the directory as a Python module.

Inside that file, the minimal Flask app is placed — for example:

```python
from flask import Flask
app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret_key123'

@app.route('/')
def index():
    return {"message": "testing"}
```

This file will act as the whole website for now.

---

### **3. Installing Flask**

Before running the app, Flask must be installed using:

```
pip3 install flask
```

Without this, Python cannot import or run the application.

---

### **4. Installing NGINX**

NGINX acts as a **reverse proxy**.
Instead of the Flask app directly serving external traffic (which is insecure and inefficient), NGINX accepts all incoming HTTP requests on the server and forwards them internally to the Flask app.

Install:

```
apt-get install nginx
```

---

### **5. Configuring NGINX**

NGINX must be told where to send browser requests. A config file is created here:

```
nano /etc/nginx/sites-enabled/flask_project
```

Inside the file, the structure looks like:

```
server {
    listen 80;
    server_name PUBLIC_SERVER_IP;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

Only one part must be changed manually:

* `PUBLIC_SERVER_IP` → replace with the actual IP shown in your cloud dashboard.

After adding this config file:

1. The default NGINX config is removed:

   ```
   unlink /etc/nginx/sites-enabled/default
   ```

2. NGINX is reloaded so changes take effect:

   ```
   nginx -s reload
   ```

At this point, NGINX is ready to route incoming traffic.

---

### **6. Installing Gunicorn**

Flask’s built-in development server is not suitable for public or production hosting, so Gunicorn is used to run the app instead.

Install:

```
apt-get install gunicorn
```

Gunicorn runs the Python web application and handles multiple requests efficiently.

---

### **7. Running the Application with Gunicorn**

Navigate to the folder **above** your project (not inside it), then run:

```
gunicorn -w 3 flask_project:app
```

Explanation:

* `-w 3` → run with 3 worker processes
  (Rule of thumb: `CPU_cores × 2 + 1`)
* `flask_project` → project folder treated as module
* `app` → the Flask instance name in `__init__.py`

If no errors appear, Gunicorn is now serving Flask internally.

---

### **8. Testing the Deployment**

Open a browser anywhere (phone, laptop, etc.) and enter:

```
http://YOUR_SERVER_PUBLIC_IP
```

If everything is correct, you should now see the JSON response from the Flask application — meaning the app is officially running on the public internet.

---





