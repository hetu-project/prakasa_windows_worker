# AGENTS.md - Developer Guide for AI Coding Agents

This document provides essential guidelines for AI coding agents working on the Parallax Windows CLI codebase.

## Project Overview

Parallax Windows CLI is a Windows development environment configuration tool written in modern C++ (C++17). It provides automated detection and installation of WSL2, CUDA toolkit, and development tools through an NSIS-built installer. The project uses CMake as its build system and follows modern C++ design patterns including CRTP, Template Method, and Command patterns.

**Platform:** Windows 10 Version 2004+ / Windows 11  
**Architecture:** x86_64  
**Language:** C++17  
**Build System:** CMake 3.6+  
**Compiler:** MSVC (Visual Studio)

## Build Commands

### Building on Windows (Primary Method)

```bash
# From the repository root
cd src/parallax
cmake -B build -S . -G "Visual Studio 17 2022" -A x64
cmake --build build --config Release

# Or use Visual Studio directly after generating project files
```

### Build Output

- Binary location: `src/parallax/build/x64/Release/parallax.exe`
- Log file: `parallax.log` (created in the same directory as the executable)

### Building on Mac/Linux (Docker Cross-Compilation)

**Note:** This is for development/testing only. Production builds should use MSVC on Windows.

```bash
# From the project root
cd docker
./build.sh

# Output: src/parallax/build-mingw/parallax.exe
```

See [docker/README.md](docker/README.md) for detailed Docker build instructions.

### Building the Installer

```bash
# From the installer directory (Windows only)
cd installer
.\build-nim.bat
# Output: installer/Output/Parallax_Setup.exe
```

### Common Build Issues

- Ensure MSVC toolchain is installed and in PATH (Windows builds)
- For Docker builds, ensure Docker is installed and running
- Unicode definitions are automatically set: `-DUNICODE -D_UNICODE`
- Windows SDK minimum version: Windows 10 SDK (0x0601)

## Testing

**Note:** This project currently has no automated test suite. Testing is done manually through:

```bash
# Run environment check
parallax.exe check

# Run with help to verify commands
parallax.exe --help
parallax.exe config --help
parallax.exe install --help
```

When adding new features, manually test all affected commands with various argument combinations.

## Code Style Guidelines

### File Organization

```
src/parallax/
├── main.cpp                 # Entry point
├── cli/                     # Command-line interface
│   ├── command_parser.*     # Command parsing and routing
│   └── commands/            # Individual command implementations
├── config/                  # Configuration management
├── environment/             # Environment installation components
├── utils/                   # Utility functions
└── tinylog/                 # Logging system
```

### Naming Conventions

- **Classes:** PascalCase (e.g., `ConfigManager`, `BaseCommand`)
- **Functions/Methods:** PascalCase (e.g., `GetInstance()`, `Execute()`)
- **Variables:** snake*case (e.g., `config_path*`, `stdout_output`)
- **Constants:** UPPER_SNAKE_CASE (e.g., `KEY_PROXY_URL`, `DEFAULT_CONFIG_PATH`)
- **Namespaces:** lowercase (e.g., `parallax`, `config`, `utils`, `commands`)
- **Private members:** Suffix with underscore (e.g., `config_values_`, `mutex_`)

### Header Files

- Always use `#pragma once` for header guards
- Include order:
  1. Corresponding header (for .cpp files)
  2. Standard library headers (`<iostream>`, `<string>`, etc.)
  3. Windows API headers (`<windows.h>`)
  4. Project headers (relative paths with quotes)

Example:

```cpp
#include "config_manager.h"      // Corresponding header
#include <fstream>               // Standard library
#include <string>
#include <windows.h>             // Windows API
#include "../tinylog/tinylog.h"  // Project headers
#include "../utils/utils.h"
```

### Includes in Headers

Minimize includes in headers. Use forward declarations when possible:

```cpp
// In header:
namespace parallax { namespace config { class ConfigManager; } }

// In implementation:
#include "config/config_manager.h"
```

### Namespaces

All code must be within the `parallax` namespace with appropriate sub-namespaces:

```cpp
namespace parallax {
namespace commands {

// Code here

}  // namespace commands
}  // namespace parallax
```

### Class Design Patterns

#### CRTP (Curiously Recurring Template Pattern)

Used for compile-time polymorphism with zero runtime overhead:

```cpp
template <typename Derived>
class BaseCommand : public ICommand {
public:
    CommandResult Execute(const std::vector<std::string>& args) override final {
        // ...
        return static_cast<Derived*>(this)->ExecuteImpl(context);
    }
};

class CheckCommand : public AdminCommand<CheckCommand> {
    CommandResult ExecuteImpl(const CommandContext& context) {
        // Implementation
    }
};
```

#### Template Method Pattern

Define execution flow in base class, specific implementation in derived:

```cpp
// Base class defines the template:
// 1. ValidateArgs → 2. PrepareEnvironment → 3. ExecuteImpl

// Derived classes override:
CommandResult ValidateArgsImpl(CommandContext& context) override;
CommandResult ExecuteImpl(const CommandContext& context) override;
void ShowHelpImpl() override;
```

### Error Handling

- Use exceptions sparingly; prefer return codes (`CommandResult` enum)
- Always log errors with `error_log()` before returning error codes
- Check return values from Windows API calls
- Provide user-friendly error messages to console via `ShowError()`

```cpp
if (!result) {
    error_log("Failed to execute command: %s", error_msg.c_str());
    ShowError("Command execution failed. Check logs for details.");
    return CommandResult::ExecutionError;
}
```

### Logging

Use the tinylog system for all logging:

```cpp
#include "tinylog/tinylog.h"

debug_log("Debug information: %s", debug_info.c_str());
info_log("Operation started: %s", operation.c_str());
warn_log("Warning: %s", warning_msg.c_str());
error_log("Error occurred: %s", error_msg.c_str());
crit_log("Critical failure: %s", critical_msg.c_str());
```

Log levels:

- `debug_log`: Verbose debugging information
- `info_log`: General information about program execution
- `warn_log`: Warning messages that don't prevent execution
- `error_log`: Error conditions that affect functionality
- `crit_log`: Critical failures requiring immediate attention

### Comments and Documentation

- Use `/** ... */` for public API documentation
- Use `//` for inline comments
- Document complex algorithms and non-obvious code
- Include parameter descriptions for non-trivial functions

```cpp
/**
 * @brief Execute command line and get output result (synchronous version)
 *
 * @param cmd Command to execute
 * @param timeout Timeout in seconds
 * @param stdout_output Standard output content
 * @param stderr_output Standard error output content
 * @return Command execution return code, <0 indicates execution failure
 */
int ExecCommandEx(const std::string& cmd, int timeout,
                  std::string& stdout_output, std::string& stderr_output);
```

### Memory Management

- Prefer stack allocation over heap when possible
- Use smart pointers (`std::unique_ptr`, `std::shared_ptr`) for dynamic allocation
- Avoid raw `new`/`delete`
- Use RAII for resource management

### String Handling

Be mindful of Windows encoding issues:

```cpp
// Use utility functions for encoding conversion
std::string utf8_str = UnicodeToUtf8(wide_str);
std::wstring wide_str = Utf8ToUnicode(utf8_str);

// PowerShell output requires special handling
std::string converted = ConvertPowerShellOutputToUtf8(raw_output);
```

### Constants and Configuration

- Define configuration keys as constants in `config_manager.h`:
  ```cpp
  extern const std::string KEY_PROXY_URL;
  ```
- Initialize in `config_manager.cpp`:
  ```cpp
  const std::string KEY_PROXY_URL = "proxy_url";
  ```

## Adding New Commands

To add a new command:

1. Create header/source files in `src/parallax/cli/commands/`
2. Inherit from appropriate base class (`BaseCommand`, `AdminCommand`, `WSLCommand`)
3. Implement required methods: `ValidateArgsImpl()`, `ExecuteImpl()`, `ShowHelpImpl()`
4. Register command in `command_parser.cpp`
5. Add files to `CMakeLists.txt` in appropriate group
6. Update README.md with command documentation

## Common Pitfalls

- **Encoding Issues:** Always convert PowerShell/WSL output using utility functions
- **Administrator Privileges:** Use `IsAdmin()` check for operations requiring elevation
- **WSL Integration:** Use `BuildWSLCommand()` helpers for proper WSL command construction
- **Thread Safety:** Use `std::lock_guard` for shared resources (e.g., ConfigManager)
- **Resource Cleanup:** Ensure proper cleanup in destructors and error paths
- **Unicode:** All strings should be UTF-8 internally; convert at boundaries

## Useful Utilities

```cpp
// Path operations
std::string exe_dir = parallax::utils::GetAppBinDir();
std::string path = parallax::utils::JoinPath(dir, filename);

// Admin check
bool is_admin = parallax::utils::IsAdmin();

// Process execution
int result = parallax::utils::ExecCommandEx(cmd, timeout, stdout_out, stderr_out);

// Configuration access
auto& config = parallax::config::ConfigManager::GetInstance();
std::string value = config.GetConfigValue(KEY_PROXY_URL);
```

## Project Structure Notes

- CMake files in `common_make/` define compilation/linking properties
- NSIS installer scripts in `installer/SetupScripts/`
- Split large source files into parts (e.g., `windows_feature_manager.cpp` and `windows_feature_manager2.cpp`)
- Group related functionality using `source_group()` in CMakeLists for Visual Studio organization

## Additional Resources

- README.md: User-facing documentation and feature overview
- CHANGELOG.md: Track all changes following semantic versioning
- LICENSE: Apache 2.0 license
