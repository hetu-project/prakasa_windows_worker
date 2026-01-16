<div align="center">

# Prakasa Windows CLI

Prakasa Windows CLI is a Windows environment configuration tool for the Prakasa decentralized P2P GPU inference network. It provides automatic detection and installation of WSL2, CUDA toolkit, and runtime dependencies required for participating in the Prakasa network. This project adopts modern C++ architecture and implements one-click deployment through NSIS-built Windows installer.


<h3>Prakasa — Decentralized P2P GPU Inference Network</h1>
  <p style="font-size:1.05em; margin-top:6px;">Prakasa (Sanskrit: प्रकाश) — The decentralized "Light" of intelligence.</p>
  <p style="max-width:900px; margin:12px auto;">
    Prakasa is a decentralized, privacy-preserving P2P GPU inference middleware built on top of the open-source Parallax engine. It leverages the Nostr protocol for resilient, censorship-resistant orchestration and integrates the RIM economic system for trustless, real-time settlements and reputation.
  </p>
</div>

## About Prakasa

Prakasa is a high-performance, privacy-centric P2P GPU inference middleware that transforms idle GPU resources into a unified, resilient intelligence layer. Key features include:

- **Nostr-Powered Orchestration**: Decentralized communication bus with no single point of failure
- **RIM Economic System**: Atomic settlements with real-time micropayments per inference
- **Privacy & Security**: End-to-end encryption, NIP-44/59 Gift Wrap for metadata protection
- **Built on Parallax**: Evolution of the open-source Parallax engine for global P2P scale

## Features

### 1. Environment Management

- **Automatic Check**: Comprehensive checking of Windows environment, WSL2, NVIDIA drivers, and Prakasa runtime
- **One-Click Installation**: Automatic installation and configuration of WSL2, CUDA toolkit, Prakasa dependencies
- **Smart Detection**: Support for NVIDIA GPU detection and CUDA toolkit verification
- **System Requirements**: Minimum support for RTX 3060 Ti, recommended RTX 4090 or higher configuration

### 2. Prakasa Network Integration

- **P2P Node Setup**: Configure your Windows machine as a Prakasa compute provider
- **Nostr Integration**: Automatic setup of Nostr relay connections for decentralized orchestration
- **RIM Wallet**: Integrated settlement system for receiving compute payments
- **Real-time Feedback**: Monitor installation progress and network connectivity status

### 3. WSL2 Integration

- **Command Execution**: Complete WSL2 command execution support for Prakasa runtime
- **Real-time Output**: Real-time stdout/stderr output for inference job monitoring
- **Interrupt Support**: Support for Ctrl+C interruption of running compute tasks
- **Proxy Synchronization**: Automatic synchronization of system proxy configuration for Nostr relay access

### 4. Configuration Management

- **Flexible Configuration**: Support for multiple configurations including proxy, WSL distribution, installation sources, etc.
- **Dynamic Updates**: Configuration changes automatically synchronized to related components
- **Reset Function**: One-click reset of all configurations to default state
- **Secure Storage**: Secure storage and access control for configuration files

### 5. Modern Architecture Design

- **Template Method Pattern**: Unified command execution flow and error handling
- **CRTP Technology**: Compile-time polymorphism with zero runtime overhead
- **Command Pattern**: Extensible command registration and execution mechanism
- **Strategy Pattern**: Flexible environment requirements and checking strategies
- **C++ Shell + Python Core**: Windows native shell forwarding to Prakasa Python runtime

## Architecture Design

Prakasa Windows CLI is a C++ native shell that manages the Prakasa Python runtime in WSL2. The architecture includes:

- **Command Line Interface** (`src/parallax/cli/`): Unified command parsing with template method pattern
- **Environment Installer** (`src/parallax/environment/`): Modular system for WSL2, CUDA, and Prakasa setup
- **Configuration Manager** (`src/parallax/config/`): Dynamic configuration loading and validation
- **Utility Library** (`src/parallax/utils/`): WSL command building, process management, GPU detection
- **Logging System** (`src/parallax/tinylog/`): Asynchronous logging with rotation support

For detailed architecture analysis, see [ARCHITECTURE_ANALYSIS.md](ARCHITECTURE_ANALYSIS.md).

## System Requirements

**Operating System**: Windows 10 Version 2004+ or Windows 11 (x86_64, Administrator privileges required)

**Hardware**:

- GPU: NVIDIA RTX 3060 Ti or higher (8GB+ VRAM, 24GB recommended)
- RAM: 16GB minimum (32GB recommended)
- Storage: 50GB+ available space
- Network: Stable internet connection

**Software** (automatically installed except CUDA):

- WSL2 + Ubuntu 24.04
- NVIDIA Driver with CUDA 12.x support
- CUDA Toolkit 12.8 or 12.9 (pre-install required)
- Prakasa Runtime (Python-based)

## Quick Start

### 1. Download and Install

Download the latest installer from the Release page:

[Prakasa_Windows_Setup.exe](https://github.com/hetu-project/prakasa_windows_cli/releases/latest/download/Prakasa_Windows_Setup.exe)

### 2. Environment Check

```cmd
prakasa check
```

### 3. Environment Installation (if needed)

```cmd
prakasa install
```

### 4. Configure Proxy (optional)

```cmd
prakasa config proxy_url "http://127.0.0.1:7890"
```

### 5. Verify Installation

```cmd
# Verify all environment components
prakasa check

# Start Prakasa compute node
prakasa run

# Join Prakasa network as provider
prakasa join

# Access chat interface (optional)
prakasa chat
```

## Command Reference

### `prakasa check`

Check environment requirements and Prakasa node readiness

```cmd
prakasa check [--help|-h]
```

### `prakasa install`

Install required environment components and Prakasa runtime

```cmd
prakasa install [--help|-h]
```

### `prakasa config`

Configuration management command

```cmd
# View all configurations
prakasa config list

# Set configuration item
prakasa config <key> <value>

# Delete configuration item
prakasa config <key> ""

# Reset all configurations
prakasa config reset

# View help
prakasa config --help
```

### `prakasa run`

Run Prakasa node (scheduler mode) directly in WSL

```cmd
prakasa run [args...]
```

### `prakasa join`

Join Prakasa P2P network as a compute provider

```cmd
prakasa join [args...]
```

### `prakasa chat`

Access Prakasa chat interface for testing inference

```cmd
prakasa chat [args...]
```

### `prakasa cmd`

Execute commands in WSL or Prakasa Python virtual environment

```cmd
prakasa cmd [--venv] <command> [args...]
```

**Command Descriptions**:

- `run`: Start Prakasa node in scheduler mode. Acts as a coordinator in the P2P network. You can pass any arguments supported by `prakasa run` command. Examples: `prakasa run -m Qwen/Qwen3-0.6B`, `prakasa run --port 8080`
- `join`: Join Prakasa P2P network as a compute provider. Your GPU will be available for decentralized inference tasks. Examples: `prakasa join -m Qwen/Qwen3-0.6B`, `prakasa join -s scheduler-addr`
- `chat`: Access chat interface for testing Prakasa inference capabilities. Examples: `prakasa chat` (local network), `prakasa chat -s scheduler-addr` (remote scheduler), `prakasa chat --host 0.0.0.0` (allow external access). After launching, visit http://localhost:3002 in your browser.
- `cmd`: Pass-through commands to WSL environment, supports `--venv` option to run in Prakasa project's Python virtual environment

**Main Configuration Items**:

- `proxy_url`: Network proxy address (supports http, socks5, socks5h) - for Nostr relay access
- `wsl_linux_distro`: WSL Linux distribution (default Ubuntu-24.04)
- `wsl_installer_url`: WSL installer download URL
- `wsl_kernel_url`: WSL2 kernel update package download URL
- `prakasa_git_repo_url`: Prakasa project Git repository URL (default: https://github.com/hetu-project/prakasa.git)

## Build Instructions

### Development Environment

- **Visual Studio 2022**: With C++ desktop development workload
- **CMake 3.20+**: For building C++ projects
- **NSIS 3.08+**: For creating Windows installer

### Quick Build

Execute in project root directory:

```cmd
cd src
mkdir build && cd build
cmake ../parallax -A x64
cmake --build . --config Release
```

Generated executable is located at: `src/build/x64/Release/prakasa.exe`

### Create Installer

```cmd
# 1. Create installation file directory
mkdir installer\FilesToInstall

# 2. Copy generated executable
copy src\build\x64\Release\prakasa.exe installer\FilesToInstall\

# 3. Build installer
call installer\build-nim-nozip.bat
```

## Configuration

Prakasa is configured through configuration files, main configuration items include:

```conf
# WSL related configuration
wsl_installer_url=https://github.com/microsoft/WSL/releases/download/2.4.13/wsl.2.4.13.0.x64.msi
wsl_kernel_url=https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
wsl_linux_distro=Ubuntu-24.04

# Prakasa project configuration
prakasa_git_repo_url=https://github.com/hetu-project/prakasa.git

# Network proxy configuration (optional, for Nostr relay access)
proxy_url=http://127.0.0.1:7890
```

## Troubleshooting

**Common Issues**:

1. **Environment Check Failed**: Run with administrator privileges, verify Windows version and NVIDIA driver
2. **WSL2 Installation Failed**: Enable Virtual Machine Platform in Windows Features, check BIOS virtualization
3. **CUDA Not Detected**: Pre-install CUDA Toolkit 12.8 or 12.9, verify with `nvidia-smi`
4. **Network Issues**: Configure proxy if needed for Nostr relay access (`prakasa config proxy_url "http://..."`)

**Diagnostic Commands**:

```cmd
prakasa check              # Full environment check
prakasa config list        # View configuration
wsl --list --verbose       # WSL status
nvidia-smi                 # GPU status
```

**Logs**: `prakasa.log` in installation directory (`C:\Program Files (x86)\Prakasa\`)

## Key Technical Features

- **Real-time Command Output**: Live stdout/stderr with Ctrl+C interrupt support
- **Automatic Proxy Sync**: Proxy configuration forwarded to WSL and Nostr relays
- **Smart GPU Detection**: Automatic NVIDIA GPU and CUDA compatibility verification
- **Comprehensive Error Handling**: Detailed diagnostics with recovery suggestions

## Development

For development guidelines, build instructions, code style, and contribution workflow, see:

- **Developer Guide**: [AGENTS.md](AGENTS.md)
- **Architecture Details**: [ARCHITECTURE_ANALYSIS.md](ARCHITECTURE_ANALYSIS.md)

## Directory Structure

```
prakasa_windows_cli/
├── src/prakasa/           # C++ source code
│   ├── cli/              # Command line interface
│   ├── environment/      # Environment installer
│   ├── config/           # Configuration manager
│   ├── utils/            # Utilities and WSL integration
│   └── tinylog/          # Logging system
├── docker/               # Cross-compilation support
├── installer/            # NSIS installer scripts
├── AGENTS.md            # Developer guide
├── ARCHITECTURE_ANALYSIS.md  # Technical architecture
└── README.md            # This file
```

## Contributing

Contributions welcome! Please fork the repository, create a feature branch, and submit a Pull Request. For issues or questions, open an issue on GitHub.

## License

See [LICENSE](LICENSE) file for details.

---

**Note**: Prakasa is a decentralized P2P GPU inference network built on the Parallax engine. It leverages Nostr protocol for resilient orchestration and RIM economic system for trustless settlements. Please ensure your hardware configuration meets the minimum requirements and that relevant drivers are correctly installed before participating in the network.

## Additional Resources

- **Prakasa Core Repository**: [github.com/hetu-project/prakasa](https://github.com/hetu-project/prakasa)
- **Architecture Analysis**: See [ARCHITECTURE_ANALYSIS.md](ARCHITECTURE_ANALYSIS.md) for detailed technical design
- **Developer Guide**: See [AGENTS.md](AGENTS.md) for development guidelines
- **Nostr Protocol**: [github.com/nostr-protocol/nostr](https://github.com/nostr-protocol/nostr)
- **Original Parallax**: [github.com/gradienthq/parallax](https://github.com/gradienthq/parallax)
