# Installing Ubuntu (Linux), Python, and VirtualBox on Windows

This guide covers two ways to run Linux on your Windows machine:

- **Part 1 — WSL2 + Ubuntu (recommended):** Lightweight, fast, deeply integrated with Windows. Best for development work, running Python, scripts, and command-line tools.
- **Part 2 — VirtualBox + Ubuntu (alternative/full VM):** A full, isolated virtual machine with a complete Ubuntu desktop. Best when you need a true GUI desktop environment or full OS isolation.

> ⚠️ **Note:** WSL2 and VirtualBox both use virtualization but rely on different underlying technology (WSL2 uses Hyper-V/Virtual Machine Platform; VirtualBox uses its own hypervisor). Running both is possible on modern Windows/VirtualBox versions, but if you hit VirtualBox boot errors, see the [Troubleshooting](#troubleshooting) section.

---

## Prerequisites

- Windows 10 (version 2004, Build 19041 or higher) or Windows 11
- Administrator access on your machine
- Virtualization enabled in BIOS/UEFI (usually enabled by default; check Task Manager → Performance → CPU → "Virtualization" should say **Enabled**)
- Stable internet connection

---

## Part 1: Install Ubuntu via WSL2 (Recommended)

### Step 1: Open PowerShell as Administrator

1. Click **Start**, type `PowerShell`
2. Right-click **Windows PowerShell** → **Run as administrator**

### Step 2: Install WSL and Ubuntu (one command)

Modern Windows lets you install everything in a single command:

```powershell
wsl --install
```

This command will:
- Enable the required Windows features (**Virtual Machine Platform** and **Windows Subsystem for Linux**)
- Download and install the latest **WSL2 Linux kernel**
- Install **Ubuntu** (the default distro) from the Microsoft Store

### Step 3: Restart your computer

```powershell
Restart-Computer
```

A restart is required to finish enabling the Windows features.

### Step 4: Launch Ubuntu and create your user account

1. After restart, click **Start**, type `Ubuntu`, and open it (or it may launch automatically)
2. Wait for installation to finish (first launch takes a minute or two)
3. When prompted, create a **UNIX username** and **password** (this is separate from your Windows login; the password won't display as you type — that's normal)

### Step 5: Update Ubuntu packages

Inside the Ubuntu terminal:

```bash
sudo apt update && sudo apt upgrade -y
```

### Step 6 (Optional): Set Ubuntu as your default WSL distro

If you plan to install multiple distros, set Ubuntu as default:

```powershell
wsl --set-default Ubuntu
```

### Step 7 (Optional): Verify WSL version

Confirm Ubuntu is running under WSL2 (not the older, slower WSL1):

```powershell
wsl -l -v
```

You should see something like:

```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

If it shows version `1`, upgrade it:

```powershell
wsl --set-version Ubuntu 2
```

---

## Part 2: Install Python on Ubuntu (WSL)

Ubuntu usually ships with Python 3 pre-installed, but it's good practice to confirm and set up tooling properly.

### Step 1: Check existing Python version

```bash
python3 --version
```

### Step 2: Install Python and essential tools

```bash
sudo apt update
sudo apt install -y python3 python3-pip python3-venv build-essential
```

### Step 3: Verify installation

```bash
python3 --version
pip3 --version
```

### Step 4: (Optional) Create a shortcut alias `python` → `python3`

By default, Ubuntu only provides the `python3` command. To use `python` instead:

```bash
sudo apt install -y python-is-python3
```

### Step 5: (Recommended) Use virtual environments for projects

Avoid installing packages globally — use a virtual environment per project:

```bash
mkdir -p ~/projects/myproject
cd ~/projects/myproject
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
```

To deactivate the environment:

```bash
deactivate
```

### Step 6 (Optional): Install a code editor that integrates with WSL

If you use **VS Code**, install it on Windows, then install the **WSL extension** (`ms-vscode-remote.remote-wsl`). From inside your Ubuntu terminal, navigate to a project folder and run:

```bash
code .
```

This opens VS Code on Windows, connected directly to your Ubuntu/WSL filesystem.

---

## Part 3: Install VirtualBox + Ubuntu (Full Virtual Machine, Alternative)

Use this route if you need a full Ubuntu **desktop environment** with a GUI, or complete isolation from Windows.

### Step 1: Download VirtualBox

1. Go to the official VirtualBox site: https://www.virtualbox.org/wiki/Downloads
2. Download the **Windows hosts** installer
3. Also download the **VirtualBox Extension Pack** (optional, adds USB 3.0, RDP support, etc.)

### Step 2: Install VirtualBox

1. Run the downloaded `.exe` installer
2. Accept defaults and proceed through the setup wizard
3. Allow the network interface installation prompts (click **Yes**/**Install** when Windows asks about network adapters)
4. Restart your computer if prompted

### Step 3: Download the Ubuntu Desktop ISO

1. Go to https://ubuntu.com/download/desktop
2. Download the latest **Ubuntu Desktop LTS** `.iso` file (e.g., Ubuntu 24.04 LTS)

### Step 4: Create a new Virtual Machine

1. Open **VirtualBox**
2. Click **New**
3. Set:
   - **Name:** Ubuntu
   - **Type:** Linux
   - **Version:** Ubuntu (64-bit)
4. Allocate **memory (RAM):** at least 4096 MB (4 GB), more if available
5. Create a **virtual hard disk**: choose **VDI**, **Dynamically allocated**, at least **25 GB**

### Step 5: Attach the Ubuntu ISO

1. Select your new VM → click **Settings** → **Storage**
2. Under **Controller: IDE**, click the empty disk icon → **Choose a disk file**
3. Select the Ubuntu `.iso` file you downloaded
4. Click **OK**

### Step 6: Configure CPU and Display (recommended)

1. **Settings → System → Processor:** allocate at least 2 CPU cores
2. **Settings → Display → Video Memory:** set to 128 MB, enable **3D Acceleration** if available

### Step 7: Start the VM and install Ubuntu

1. Select the VM → click **Start**
2. The Ubuntu installer will boot from the ISO — choose **Install Ubuntu**
3. Follow the on-screen steps: language, keyboard layout, updates/other software, installation type (**Erase disk and install Ubuntu** — this only affects the virtual disk, not your real Windows disk), timezone, and create a user account
4. Wait for installation to complete, then click **Restart Now**
5. When prompted, remove the installation media (VirtualBox usually does this automatically) and press **Enter**

### Step 8: Install VirtualBox Guest Additions (recommended)

Inside the running Ubuntu VM, this enables better display resolution, shared clipboard, and shared folders:

1. In the VM window menu, click **Devices → Insert Guest Additions CD Image**
2. Open a terminal inside Ubuntu and run:

```bash
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
cd /media/$USER/VBox_GAs_*
sudo ./VBoxLinuxAdditions.run
```

3. Reboot the VM:

```bash
sudo reboot
```

### Step 9: Install Python inside the VirtualBox Ubuntu VM

Same as Part 2 — open a terminal in the Ubuntu desktop and run:

```bash
sudo apt update
sudo apt install -y python3 python3-pip python3-venv build-essential
python3 --version
```

---

## Verification Checklist

| Component | Check Command | Expected Result |
|---|---|---|
| WSL installed | `wsl -l -v` (PowerShell) | Lists Ubuntu, VERSION 2 |
| Ubuntu (WSL) running | `wsl` (PowerShell) | Drops into Ubuntu shell |
| Python (WSL/Ubuntu) | `python3 --version` | e.g. `Python 3.12.x` |
| pip | `pip3 --version` | Shows pip version |
| VirtualBox installed | Open VirtualBox app | App launches, shows Manager window |
| VirtualBox Ubuntu VM | Start VM | Boots to Ubuntu desktop login |

---

## Troubleshooting

**`wsl --install` fails or "WSL 2 requires an update to its kernel component"**
- Download and run the kernel update package manually: https://aka.ms/wsl2kernel
- Then retry `wsl --install`

**Virtualization not enabled / VirtualBox won't start a VM ("VT-x is not available")**
- Reboot into BIOS/UEFI (usually `F2`, `F10`, `Del`, or `Esc` during boot) and enable **Intel VT-x** or **AMD-V**
- If WSL2/Hyper-V is enabled, VirtualBox VMs may fail to start with a similar error. Fixes:
  - Use VirtualBox 6.1+ or 7.x, which support running alongside Hyper-V, or
  - Disable Hyper-V-based features temporarily:
    ```powershell
    bcdedit /set hypervisorlaunchtype off
    ```
    (Restart required; re-enable with `bcdedit /set hypervisorlaunchtype auto` if you need WSL2 again)

**Ubuntu app doesn't open after `wsl --install`**
- Open **Microsoft Store**, search "Ubuntu", and click **Open**/**Install** manually

**Slow file access in WSL**
- Keep your project files inside the Linux filesystem (e.g. `~/projects`) rather than under `/mnt/c/...` for best performance

**`python3: command not found` after fresh Ubuntu install**
- Run `sudo apt update && sudo apt install -y python3`

---

## Summary

- **For everyday development (recommended):** Use **WSL2 + Ubuntu** (Part 1) — faster, lighter, integrates with Windows tools like VS Code.
- **For a full desktop GUI or strict isolation:** Use **VirtualBox + Ubuntu** (Part 3).
- Install **Python 3** the same way in either environment via `apt install python3 python3-pip python3-venv`.
