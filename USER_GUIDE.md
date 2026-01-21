# Prakasa Windows User Installation Guide

## 📋 Table of Contents

- [Quick Overview](#quick-overview)
- [System Requirements](#system-requirements)
- [Installation Steps](#installation-steps)
- [Usage Instructions](#usage-instructions)
- [FAQ](#faq)
- [Troubleshooting](#troubleshooting)

---

## 🚀 Quick Overview

### What is Prakasa?

Prakasa is a decentralized P2P GPU inference network that allows you to contribute your idle GPU resources to a distributed AI inference network and earn rewards.

### What Does Prakasa Windows CLI Do?

This is a **Windows one-click installation tool** that helps you:

- ✅ Automatically detect and configure Windows environment
- ✅ Automatically install WSL2 (Windows Subsystem for Linux)
- ✅ Automatically configure CUDA and GPU drivers
- ✅ Automatically deploy Prakasa runtime environment
- ✅ Provide simple command-line interface

### How It Works

```
Windows User
    ↓
prakasa.exe (C++ Installation Tool)
    ↓
Automatically Install WSL2 + Ubuntu
    ↓
Automatically Deploy Prakasa Python Runtime
    ↓
Launch GPU Inference Node
```

**In simple terms**: This is a Windows shell program that automatically configures all environments for you, ultimately running the Prakasa Python program in a Linux environment.

---

## 💻 System Requirements

### Prerequisites

| Item                 | Requirement                                     |
| -------------------- | ----------------------------------------------- |
| **Operating System** | Windows 10 Version 2004+ or Windows 11 (64-bit) |
| **Permissions**      | Administrator privileges (required)             |
| **GPU**              | NVIDIA RTX 3060 Ti or higher                    |
| **VRAM**             | 8GB+ (24GB recommended)                         |
| **RAM**              | 16GB+ (32GB recommended)                        |
| **Disk Space**       | 50GB+ available space                           |
| **Network**          | Stable internet connection                      |

### Software Dependencies (Automatically Handled by Installer)

- ✅ **WSL2** (Windows Subsystem for Linux 2) - Automatically installed
- ✅ **Ubuntu 24.04** - Automatically installed
- ✅ **Python 3.x** - Automatically installed
- ✅ **CUDA Toolkit 12.8** - Automatically installed in WSL
- ⚠️ **NVIDIA Driver** - Must support CUDA 12.x (please install in advance)

### ⚠️ Important: NVIDIA Driver Must Be Pre-installed (Skip if already installed)

**Please install NVIDIA driver with CUDA 12.x support before running the installer**:

1. Visit [NVIDIA Driver Downloads](https://www.nvidia.com/Download/index.aspx)
2. Select your GPU model
3. Download and install the latest driver (version ≥ 525.x, with CUDA 12.x support)
4. Restart your computer after installation

**Note**: The installer will automatically install CUDA Toolkit in WSL. You only need to install the NVIDIA driver on Windows.

---

## 📦 Installation Steps

### Step 1: Download Installer

Download the latest version from GitHub Release page:

**Download Link**: [Prakasa_Win_Setup.exe](https://github.com/hetu-project/prakasa_windows_worker/releases/latest/download/Prakasa_Win_Setup.exe)

### Step 2: Run Installer

1. **Right-click** `Prakasa_Win_Setup.exe`
2. Select **"Run as administrator"**
3. Follow the installation wizard (default installation path: `C:\Program Files (x86)\Prakasa\`)

### Step 3: Open Command Prompt (Administrator Mode)

1. Press `Win + X`
2. Select **"Windows PowerShell (Admin)"** or **"Command Prompt (Admin)"**

### Step 4: Environment Check

```cmd
prakasa check
```

This command will check:

- ✅ Windows version and permissions
- ✅ Virtualization enabled
- ✅ WSL2 installed
- ✅ NVIDIA GPU and drivers
- ✅ CUDA Toolkit version

### Step 5: Automatic Environment Installation (If Needed)

If the check finds missing components, run:

```cmd
prakasa install
```

The installer will automatically:

1. Enable Windows virtualization features
2. Download and install WSL2 kernel update
3. Install Ubuntu 24.04 distribution
4. Clone Prakasa Python project
5. Create Python virtual environment
6. Install all Python dependencies

**Estimated Time**: 10-30 minutes (depending on network speed)

### Step 6: Configure Proxy (Optional, Recommended for Users in China)

If you're in mainland China, it's recommended to configure a proxy for faster access:

```cmd
# Configure network proxy (for accessing GitHub and downloading dependencies)
prakasa config proxy_url "http://127.0.0.1:7890"

# Configure PyPI mirror (for faster Python package installation)
prakasa config pip_index_url "https://pypi.tuna.tsinghua.edu.cn/simple/"
```

### Step 7: Verify Installation

Run the check command again to ensure all components are ready:

```cmd
prakasa check
```

If you see **"✅ All checks passed"**, congratulations on successful installation!

---

## 🎮 Usage Instructions

### Launch Prakasa Node (Scheduler Mode)

```cmd
prakasa run -m Qwen/Qwen3-0.6B
```

### Join P2P Network (Provide Computing Power)

```cmd
prakasa join -m Qwen/Qwen3-0.6B
```

### Launch Chat Interface (Test Inference)

```cmd
prakasa chat
```

Then visit in browser: `http://localhost:3002`

### View Configuration

```cmd
prakasa config list
```

### Reset Configuration

```cmd
prakasa config reset
```

### Execute Custom Commands in WSL

```cmd
# Execute any command in WSL
prakasa cmd ls -la

# Execute command in Prakasa Python virtual environment
prakasa cmd --venv python --version
```

---

## ❓ FAQ

### Q1: Installer Prompts "Administrator Privileges Required"

**A**: Must run as administrator:

- Right-click `prakasa.exe`
- Select **"Run as administrator"**

### Q2: Prompt "Windows Version Too Low"

**A**: Prakasa requires Windows 10 Version 2004 or higher:

- Press `Win + R`, type `winver`, check version number
- If version is below 2004, upgrade system through Windows Update

### Q3: Check Prompts "Virtualization Not Enabled"

**A**: Need to enable virtualization in BIOS:

1. Restart computer, enter BIOS (usually press F2, F12, Del, etc.)
2. Find **Virtualization Technology** or **Intel VT-x** / **AMD-V**
3. Set to **Enabled**
4. Save and exit BIOS

### Q4: Prompt "CUDA Toolkit Not Installed"

**A**: The installer will automatically install CUDA Toolkit in WSL. If you see this error after installation:

1. Run `prakasa install` again to retry CUDA Toolkit installation
2. Check if NVIDIA driver is properly installed: run `nvidia-smi` in Windows
3. Ensure your NVIDIA driver supports CUDA 12.x (version ≥ 525.x)
4. Check WSL CUDA installation: `prakasa cmd /usr/local/cuda-12.8/bin/nvcc --version`

### Q5: `prakasa install` Fails

**Possible Causes and Solutions**:

| Error Message                      | Solution                                                                                          |
| ---------------------------------- | ------------------------------------------------------------------------------------------------- |
| WSL installation failed            | Check network connection, manually download WSL installer                                         |
| Ubuntu download failed             | Configure proxy: `prakasa config proxy_url "http://..."`                                          |
| Python package installation failed | Configure PyPI mirror: `prakasa config pip_index_url "https://pypi.tuna.tsinghua.edu.cn/simple/"` |
| Git clone failed                   | Check firewall, configure proxy or change Git mirror source                                       |

### Q6: Runtime Prompts "NVIDIA Driver Not Found"

**A**: Please install NVIDIA driver supporting CUDA 12.x:

1. Visit [NVIDIA Driver Downloads](https://www.nvidia.com/Download/index.aspx)
2. Select your GPU model
3. Download and install latest driver (version ≥ 525.x)
4. Restart computer

### Q7: Network Connection Issues (Users in Mainland China)

**A**: Recommended to configure proxy:

```cmd
# Configure HTTP proxy (assuming local proxy port 7890)
prakasa config proxy_url "http://127.0.0.1:7890"

# Or SOCKS5 proxy
prakasa config proxy_url "socks5://127.0.0.1:1080"

# Configure PyPI mirror acceleration
prakasa config pip_index_url "https://pypi.tuna.tsinghua.edu.cn/simple/"
```

### Q8: How to Update Prakasa?

**A**: Re-download and install the latest `Prakasa_Win_Setup.exe`, the installer will automatically update.

### Q9: How to Uninstall?

**A**:

1. Uninstall Prakasa through Windows **"Control Panel → Programs and Features"**
2. (Optional) Manually delete WSL distribution: `wsl --unregister Ubuntu-24.04`

### Q10: Where Are Log Files?

**A**: Log files are located at:

- **Windows side**: `C:\Program Files (x86)\Prakasa\prakasa.log`
- **WSL side**: Python logs in `~/prakasa/` directory

---

## 🔧 Troubleshooting

### Step 1: View Detailed Errors

Check detailed error messages in terminal output when running commands.

### Step 2: Check Log Files

```cmd
# Windows log
type "C:\Program Files (x86)\Prakasa\prakasa.log"

# WSL log (if WSL is installed)
prakasa cmd cat ~/prakasa/prakasa.log
```

### Step 3: Verify Basic Environment

```cmd
# Check WSL status
wsl --list --verbose

# Check NVIDIA driver
nvidia-smi

# Check CUDA version
prakasa cmd /usr/local/cuda-12.8/bin/nvcc --version
```

### Step 4: Manually Test WSL

```cmd
# Enter WSL
wsl -d Ubuntu-24.04

# Check Python environment
cd ~/prakasa
source ./venv/bin/activate
python --version
prakasa --help
```

### Step 5: Reinstall

If none of the above steps resolve the issue, try a complete reinstall:

```cmd
# 1. Uninstall Prakasa Windows CLI
# Uninstall through Control Panel

# 2. Delete WSL distribution
wsl --unregister Ubuntu-24.04

# 3. Re-download and install Prakasa_Win_Setup.exe
```

### Get Help

If the issue is still unresolved, please submit an Issue on GitHub:

📝 **Issue Template**:

```
**Environment Information**:
- Windows Version: (run `winver` to check)
- GPU Model: (run `nvidia-smi` to check)
- CUDA Version: (run `prakasa cmd /usr/local/cuda-12.8/bin/nvcc --version` to check)
- Prakasa Version: (check installer version)

**Problem Description**:
(Detailed description of the issue encountered)

**Error Logs**:
(Paste relevant content from prakasa.log)

**Attempted Solutions**:
(List the steps you have already tried)
```

---

## 📚 Architecture Description (For Technical Users)

### How It Works

Prakasa Windows CLI adopts **"C++ Shell + Python Core"** architecture:

```
┌──────────────────────────────────────┐
│  Windows User Interface               │
│  prakasa.exe (C++ CLI)               │
│  - Environment detection & config     │
│  - WSL2 management                    │
│  - Command forwarding                 │
└────────────┬─────────────────────────┘
             │
             ▼
┌──────────────────────────────────────┐
│  WSL2 (Ubuntu 24.04)                 │
│  ~/prakasa/                          │
│  - Python virtual environment         │
│  - Prakasa Python core                │
│  - GPU inference engine               │
└──────────────────────────────────────┘
```

### Why This Architecture?

1. **Windows User-Friendly**: One-click installation, no manual WSL2 and Python environment configuration needed
2. **System Integration**: Requires Windows API to enable system features and manage permissions
3. **Performance Optimization**: C++ Shell launches quickly, GPU computation handled by Python/CUDA
4. **Environment Isolation**: Windows side manages system, Linux side handles inference computation

### Dependency Components

| Component      | Version       | Purpose                 |
| -------------- | ------------- | ----------------------- |
| Windows        | 10 2004+ / 11 | Host operating system   |
| WSL2           | 2.0+          | Linux subsystem         |
| Ubuntu         | 24.04         | Linux distribution      |
| NVIDIA Driver  | 525.x+        | GPU driver              |
| CUDA Toolkit   | 12.8/12.9     | GPU computing framework |
| Python         | 3.10+         | Prakasa runtime         |
| Prakasa Python | Latest        | Inference engine core   |

---

## 📖 Additional Documentation

- **Technical Architecture Details**: [ARCHITECTURE.md](ARCHITECTURE.md)
- **Developer Guide**: [AGENTS.md](AGENTS.md)
- **Complete Feature Documentation**: [README.md](README.md)
- **Prakasa Core Project**: [github.com/hetu-project/prakasa](https://github.com/hetu-project/prakasa)

---

## 📞 Support

- **Issue Reporting**: [GitHub Issues](https://github.com/hetu-project/prakasa_windows_worker/issues)
- **Project Home**: [GitHub Repository](https://github.com/hetu-project/prakasa_windows_worker)

---

**Thank you for using Prakasa! Let's build a decentralized AI inference network together 🚀**
