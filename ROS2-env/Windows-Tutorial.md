# ROS2 Development Environment Setup
## using WSL and Docker
### Workshop Booklet

---

# 1. Introduction

Welcome to this workshop! By the end of this session you will have a fully working Linux ROS2 robotics environment running directly on your Windows computer — no dual boot, no complicated setup, no wiping your hard drive.

We will do this using two tools: WSL and Docker. You do not need to know anything about either of them to get started — this booklet will walk you through every step and explain what is happening along the way.

---

## What is WSL?

WSL stands for Windows Subsystem for Linux. In plain English, it is a feature built into Windows that lets you run a real Linux terminal directly inside Windows — like having a mini Linux computer living inside your Windows machine.

Why do we need this? ROS2 (the Robot Operating System) is designed for Linux. Rather than forcing you to install Linux as your main operating system or buy a separate computer, WSL lets us run everything Linux needs in a window on your existing PC.

Windows 11 also includes a feature called **WSLg** — Windows Subsystem for Linux GUI. This means graphical Linux applications like RViz and Gazebo will appear as normal windows on your desktop with no extra configuration needed. This is essential for the visualisation and simulation work in this course.

> 💡 **Think of it like this:** Your Windows computer is a house. WSL opens a door to a separate room inside that house that runs entirely on Linux. You can walk between the two rooms freely — copy files, run programs, and switch back to Windows whenever you like.

---

## What is Docker?

Docker is like a vending machine for pre-built computer environments. Instead of spending hours installing ROS2 and all its dependencies one by one, someone has already packaged everything into a neat "container." You just download it and run it — like installing an app, but for an entire Linux environment.

> 💡 **Think of it like this:** A Docker **image** is the recipe. A Docker **container** is the meal cooked from that recipe. Every time you run the container you get a fresh, identical environment — no matter whose laptop you are on. This means every student in this class will have exactly the same setup.

---

## Overview

- Install WSL — enable the Linux engine inside Windows
- Install Docker Desktop — used to build your environment
- Build the ROS2 robotics environment — from the lab repository
- Import it into WSL — creating your dedicated robotics workstation
- Set up a Windows Terminal profile — launch everything with one click
- Verify it all works — confirm ROS2 and graphical apps are ready

**Goal:** By the end of this workshop, participants will have a ready-to-use, persistent Linux ROS2 environment running on Windows — launchable from their terminal in one click.

---

# 2. Requirements

Before we start, make sure your computer meets the following requirements. If you are unsure about any of these, ask a demonstrator before proceeding.

- **Windows 11** — required for graphical applications to work
- Administrator access to your computer
- A working internet connection
- 10–20 GB of free storage space

> ⚠️ **Windows 11 is required.** Graphical applications like RViz and Gazebo rely on WSLg which is built into Windows 11. If you are on Windows 10 please speak to a demonstrator.

> ⚠️ **Not sure which Windows version you have?** Press the Windows key + R, type `winver`, and press Enter. A window will pop up showing your exact Windows version.

---

# 3. Install WSL

WSL is built into Windows — we just need to turn it on. We are installing the WSL2 engine only — we do not need a default Linux distribution at this stage because we will be building our own purpose-built environment shortly.

### Step 1 — Open PowerShell as Administrator

PowerShell is Windows' built-in command-line tool. We need to open it with Administrator privileges so it has permission to install system features.

1. Click the Start menu (Windows key)
2. Type "PowerShell"
3. Right-click **Windows PowerShell** and select **"Run as Administrator"**
4. Click **"Yes"** when Windows asks for permission

Once open, navigate to your user folder — this ensures all commands run from the right place:

```powershell
cd C:\Users\$env:USERNAME
```

> ⚠️ **Important:** You must open PowerShell **as Administrator** — not just regular PowerShell. If you skip this, the install command will fail with a permissions error.

### Step 2 — Run the install command

In the PowerShell window, type the following and press Enter:

```powershell
wsl --install --no-distribution
```

> 💡 The `--no-distribution` flag tells WSL to install the engine only, without downloading a default Linux distro. We do not need a generic Ubuntu — we will be importing our own purpose-built robotics environment instead.

This will enable the WSL feature and install the WSL2 kernel. The output will look something like this:

```
Installing: Windows Subsystem for Linux
Windows Subsystem for Linux has been installed.
```

### Step 3 — Restart your computer

WSL requires a full system restart to finish installing. Save any open work and restart before continuing.

### Step 4 — Verify WSL is working

After restarting, open PowerShell and run:

```powershell
wsl --status
```

You should see:

```
Default Version: 2
```

> ✅ **If you see Default Version: 2, WSL2 is installed and ready.**

> ⚠️ **Troubleshooting:** If the install command did not work, some machines need the Virtual Machine Platform feature enabled manually. Open PowerShell as Administrator and run these two commands, then restart:
> ```powershell
> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
> ```

---

# 4. Install Docker Desktop

Now that WSL is running we can install Docker Desktop. Docker Desktop is the application we will use to build our robotics environment. It runs on Windows and uses the WSL2 engine we just installed under the hood.

### Step 1 — Download Docker Desktop

Open a web browser and go to **https://www.docker.com/products/docker-desktop**

Click **"Download for Windows"** and run the installer when it finishes downloading.

> ✅ **During installation, make sure this option is checked:** "Use WSL 2 instead of Hyper-V" — this ensures Docker uses the WSL2 engine we just installed.

### Step 2 — Complete the installation and launch Docker Desktop

Follow the installer prompts and click Finish. Docker Desktop will launch automatically. It may take a minute or two to fully start up for the first time — you will see a loading animation in the Docker Desktop window.

> ⚠️ **You may see a "WSL startup error" popup when Docker first opens.** This is normal on some machines — Docker is setting up its own internal components. To fix it:
> 1. Fully quit Docker Desktop (right-click the Docker icon in the system tray → Quit)
> 2. Open PowerShell as Administrator and run:
> ```powershell
> wsl --unregister docker-desktop
> wsl --unregister docker-desktop-data
> ```
> 3. Relaunch Docker Desktop and wait for it to fully start up.

---

# 5. Verify Docker Works

Before we build the full robotics environment, let's confirm Docker is working correctly. We will do this by running a small test container called "hello-world."

### Step 1 — Open PowerShell

Open PowerShell — it does not need to be Administrator this time.

### Step 2 — Run the Hello World container

Type the following and press Enter:

```powershell
docker run hello-world
```

Docker will:
1. Look for the "hello-world" image on your machine
2. Not find it locally, so automatically download it from the internet
3. Create a container from the image and run it
4. Print a message, then stop

You should see output that includes:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

> ✅ **If you see "Hello from Docker!" — Docker is working correctly.**

### Step 3 — Check Docker Desktop

Switch over to the **Docker Desktop** app. Click **"Images"** in the left sidebar — you should see `hello-world` listed there.

Now click **"Containers"** — you will see a stopped container that ran moments ago.

![Hello World View](images/helloWorld.png)

> 💡 **What is the difference between an Image and a Container?**
> An **Image** is the blueprint — a read-only package containing everything needed to run an environment.
> A **Container** is a running (or stopped) instance created from that image.
> You can run many containers from the same image, just like printing many copies from the same template. When a container stops it disappears — but the image stays on your machine so you can run it again anytime without re-downloading.

---

# 6. Building Your Robotics Lab Environment

You now have WSL2 and Docker Desktop installed on Windows. We are going to use Docker to build your ROS2 environment and import it into WSL as your permanent dedicated robotics workstation.

From this point all commands are run in **PowerShell** unless told otherwise.

---

### Step 1 — Install Git

We need Git to download the lab repository. Check if it is already installed by running:

```powershell
git --version
```

If you see a version number, Git is already installed — skip to Step 2.

If you see an error, run the following command to install it:

```powershell
winget install --id Git.Git -e --source winget
```

> 💡 `winget` is Windows' built-in package manager — it downloads and installs software directly from the command line without needing to visit a website. Once it finishes, close and reopen PowerShell.

Once reopened, verify Git installed correctly:

```powershell
git --version
```

You should see something like:

```
git version 2.44.0.windows.1
```

> ✅ **If you see a version number, Git is ready.**

---

### Step 2 — Clone the lab repository

```powershell
git clone https://github.com/UCROBO/foundations-of-robotics-labs.git
cd foundations-of-robotics-labs
```

> 💡 `git clone` downloads the entire project from GitHub onto your computer. `cd` moves you into that folder — it stands for "change directory."

---

### Step 3 — Build the Docker image

```powershell
docker build -t frlab -f .binder/Dockerfile.wsl .
```

> 💡 **Breaking this down:**
> - `docker build` tells Docker to build a new image
> - `-t frlab` gives it the name "frlab"
> - `-f Dockerfile.wsl` points to our WSL-specific Dockerfile
> - `.` means use the current folder

This will take 10–20 minutes depending on your internet connection. You will see a lot of output scrolling past — this is normal. Wait until you see:

```
Successfully built ...
Successfully tagged frlab:latest
```

> ✅ **The image is built and ready.**

---

### Step 4 — Export the image to a file

```powershell
$containerId = docker create frlab
docker export $containerId -o C:\Users\$env:USERNAME\frlab.tar
```

> 💡 Docker and WSL speak different formats — this step converts the Docker image into a single file that WSL can import. The first command creates a container from the image and saves its ID. The second exports the entire filesystem to a file. `$env:USERNAME` automatically fills in your Windows username.

This may take a minute or two with no progress shown — this is normal. When your prompt returns it is done.

---

### Step 5 — Import into WSL as RoboticsLab

```powershell
New-Item -ItemType Directory -Path C:\Users\$env:USERNAME\AppData\Local\RoboticsLab
wsl --import RoboticsLab C:\Users\$env:USERNAME\AppData\Local\RoboticsLab C:\Users\$env:USERNAME\frlab.tar --version 2
```

> 💡 **Breaking this down:**
> - `New-Item` creates the folder where WSL will store your distro — we put it in AppData alongside where WSL normally stores distros
> - `wsl --import` creates a new WSL distro from the file we just exported
> - `RoboticsLab` is the name we are giving it — this is what appears in Windows Terminal
> - `--version 2` ensures it uses WSL2

---

### Step 6 — Verify it worked

```powershell
wsl --list --verbose
```

You should see:

```
  NAME            STATE           VERSION
  RoboticsLab     Stopped         2
```

> ✅ **Your RoboticsLab environment has been created.**

---

### Step 7 — Test ROS2 is installed

Open your new environment:

```powershell
wsl -d RoboticsLab
```

You are now inside your Linux workstation. Run:

```bash
ros2 --help
```

If you see a list of ROS2 commands, ROS2 is installed and working.

> ✅ **ROS2 is installed and working.**

Type `exit` to return to PowerShell.

---

### Step 8 — Test a graphical application

Let's confirm WSLg is working by launching a graphical ROS2 tool:

```powershell
wsl -d RoboticsLab
```

```bash
rqt
```

A graphical window should appear on your Windows desktop.

> ✅ **If you see the rqt window — graphical applications are working perfectly via WSLg.**

Type `exit` when done.

---

### Step 9 — Clean up

Remove the tar file to free up space:

```powershell
Remove-Item C:\Users\$env:USERNAME\frlab.tar
```

Remove the Docker image — it has done its job:

```powershell
docker rmi frlab
```

> 💡 Your RoboticsLab environment is completely self-contained and does not need Docker to run. Docker stays installed on your machine for use later in the course.

---

### Step 10 — Set up your Windows Terminal profile

Now we give your environment its own named tab in Windows Terminal.

1. Open Windows Terminal
2. Click the **dropdown arrow** (˅) at the top
3. Click **Settings**
4. Click **"Add a new profile"** in the left sidebar
5. Click **"New empty profile"**

Fill in the following:

| Field | Value |
|---|---|
| Name | `Robotics Lab` |
| Command line | `wsl.exe -d RoboticsLab` |
| Starting directory | `\\wsl$\RoboticsLab\root` |

6. Click **Save**

> 💡 The command line is just `wsl.exe -d RoboticsLab` — "open WSL and boot the RoboticsLab distro." It boots straight into your environment with no extra commands needed.

---

### Step 11 — Open your Robotics Lab

Click the dropdown in Windows Terminal and select **Robotics Lab.**

You will land directly in your dedicated ROS2 environment:

```
root@LAPTOP-XXXXX:~#
```

> ✅ **This is your persistent robotics workstation. Everything you do here is saved permanently and will be exactly as you left it every time you open it.**

---

## What if something breaks?

If your environment ever gets into a bad state, rebuilding it is straightforward. Simply repeat Steps 3–9 of this section — rebuild the Docker image, export it, and re-import it. A completely fresh working environment in minutes, without touching anything else on your computer.

---