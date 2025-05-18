# advanced-physics-engine - build

*This documentation is from the private repository advanced-physics-engine.*

---

# Building the Physics Engine

This document provides detailed instructions for building the physics engine from source in different environments and configurations.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Building on Linux](#building-on-linux)
3. [Building on macOS](#building-on-macos)
4. [Building on Windows](#building-on-windows)
5. [Build Configurations](#build-configurations)
6. [Dependencies](#dependencies)
7. [Troubleshooting](#troubleshooting)

## Prerequisites

The physics engine requires the following:

- **C++ Compiler**: C++17 compatible compiler
  - GCC 7.0 or higher
  - Clang 5.0 or higher
  - MSVC 2019 or higher
- **Build System**: CMake 3.14 or higher
- **Linear Algebra**: Eigen 3.3 or higher
- **Optional**: OpenMP for parallelization

## Building on Linux

### Ubuntu/Debian

```bash
# Install dependencies
sudo apt update
sudo apt install build-essential cmake libstdc++-dev libeigen3-dev

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake ..
make -j$(nproc)

# Run tests
ctest
```

### Fedora/RHEL/CentOS

```bash
# Install dependencies
sudo dnf install gcc-c++ cmake eigen3-devel

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake ..
make -j$(nproc)

# Run tests
ctest
```

### Arch Linux

```bash
# Install dependencies
sudo pacman -S base-devel cmake eigen

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake ..
make -j$(nproc)

# Run tests
ctest
```

## Building on macOS

### Using Homebrew

```bash
# Install dependencies
brew install cmake eigen

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake ..
make -j$(sysctl -n hw.ncpu)

# Run tests
ctest
```

### Using MacPorts

```bash
# Install dependencies
sudo port install cmake eigen3

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake ..
make -j$(sysctl -n hw.ncpu)

# Run tests
ctest
```

## Building on Windows

### Using Visual Studio

1. Install Visual Studio 2019 or newer with C++ development tools
2. Install CMake from https://cmake.org/download/
3. Install Eigen:
   - Option 1: Download from http://eigen.tuxfamily.org
   - Option 2: Install via vcpkg: `vcpkg install eigen3`

```batch
:: Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

:: Create build directory
mkdir build
cd build

:: Configure and generate Visual Studio solution
cmake .. -G "Visual Studio 16 2019" -A x64

:: Build using Visual Studio
cmake --build . --config Release

:: Run tests
ctest -C Release
```

### Using MSYS2/MinGW

```bash
# Install dependencies
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-cmake mingw-w64-x86_64-eigen3

# Clone the repository (if needed)
git clone https://github.com/yourusername/physics-engine.git
cd physics-engine

# Create build directory
mkdir -p build && cd build

# Configure and build
cmake .. -G "MSYS Makefiles"
make -j$(nproc)

# Run tests
ctest
```

## Build Configurations

The physics engine supports various build configurations:

### Debug Build

```bash
cmake .. -DCMAKE_BUILD_TYPE=Debug
```

Debug builds include:
- No optimizations
- Debug symbols
- Additional runtime checks
- Verbose logging

### Release Build

```bash
cmake .. -DCMAKE_BUILD_TYPE=Release
```

Release builds include:
- Full optimizations
- No debug symbols
- Disabled assertions
- Minimal logging

### RelWithDebInfo Build

```bash
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

RelWithDebInfo builds include:
- Optimizations
- Debug symbols
- Some assertions
- Standard logging

## Configuration Options

The physics engine provides several configuration options:

```bash
# Enable/disable features
cmake .. -DPHYSICS_ENGINE_BUILD_TESTS=ON          # Build test suite
cmake .. -DPHYSICS_ENGINE_BUILD_EXAMPLES=ON       # Build example applications
cmake .. -DPHYSICS_ENGINE_ENABLE_MULTITHREADING=ON # Enable multithreading
cmake .. -DPHYSICS_ENGINE_ENABLE_DETERMINISTIC=OFF # Enable deterministic mode

# Specify Eigen path if not in standard location
cmake .. -DEIGEN3_INCLUDE_DIR=/path/to/eigen
```

## Dependencies

### Eigen

Eigen is a C++ template library for linear algebra that's required for the physics engine.

If Eigen is not installed in a standard location, you can specify its location:

```bash
cmake .. -DEIGEN3_INCLUDE_DIR=/path/to/eigen
```

### Optional Dependencies

- **OpenMP**: For parallel computation
- **GoogleTest**: For unit testing (automatically downloaded if not found)
- **Doxygen**: For API documentation generation

## Troubleshooting

### CMake Can't Find Eigen

If CMake can't find Eigen, you can specify its location:

```bash
cmake .. -DEIGEN3_INCLUDE_DIR=/path/to/eigen
```

### Compilation Errors

#### Missing C++17 Features

Error: `error: static_assert failed due to requirement 'false' "This library requires C++17"`

Solution: Make sure your compiler supports C++17 and set the C++ standard:

```bash
cmake .. -DCMAKE_CXX_STANDARD=17
```

#### Eigen-Related Errors

Error: `fatal error: Eigen/Core: No such file or directory`

Solution: Install Eigen or specify its location:

```bash
cmake .. -DEIGEN3_INCLUDE_DIR=/path/to/eigen
```

### Linker Errors

If you encounter linker errors, ensure all dependencies are properly installed and that you're using compatible library versions.

Common issues:
- Missing library files
- ABI incompatibility between libraries
- Mixing debug and release builds

### Performance Issues

If the built physics engine performs poorly:
- Ensure you're using a Release build
- Check that SIMD instructions are enabled
- Verify that multithreading is enabled
