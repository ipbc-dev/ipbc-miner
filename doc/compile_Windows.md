# Compile **bittube-miner** for Windows

## Install Dependencies

### Preparation

- Open a command line (Windows key + r) and enter `cmd`
- Execute `mkdir C:\bittube-miner-dep`

### Visual Studio Community 2017

- Download and install [Visual Studio Community 2017](https://www.visualstudio.com/downloads/)
- During install choose following components:
  - `Desktop development with C++` (left side)
  - `VC++ 2015.3 v140 toolset for desktop` (right side - **NOT** needed for CUDA 9 or AMD GPU)
  - Since release of VS2017 15.5 (12/04/17), require `VC++ 2017 version 15.4 v14.11 toolset` (under tab `Individual Components`, section `Compilers, build tools, and runtimes`), as CUDA 9.x is not compatible with compiler 14.12.X

### CMake for Win64

- Download and install latest version from https://cmake.org/download/
- Tested version: [cmake 3.9](https://cmake.org/files/v3.9/cmake-3.9.0-rc3-win64-x64.msi)
- During install choose option: `Add CMake to the system PATH for all users`

### Cuda 8.0+ (only needed for NVIDIA GPUs)

- Download and install https://developer.nvidia.com/cuda-downloads
- For minimal install choose `Custom installation options` during the install and select
    - CUDA/Development
    - CUDA/Visual Studio Integration (ignore the warning during the install that VS2017 is not supported)
    - CUDA/Runtime
    - Driver components

### AMD APP SDK 3.0 (only needed for AMD GPUs)

- Download and install the latest version from https://www.dropbox.com/s/gq8vqhelq0m6gj4/AMD-APP-SDKInstaller-v3.0.130.135-GA-windows-F-x64.exe
  (do not wonder why it is a link to a dropbox but AMD has removed the SDK downloads, see https://community.amd.com/thread/222855)

### Dependencies OpenSSL/Hwloc and Microhttpd
- For CUDA 8*:
  - Download version 1 of the precompiled binary from https://github.com/fireice-uk/bittube-miner-dep/releases/download/v1/bittube-miner-dep.zip
  - Version 1 of the pre-compiled dependencies is not compatible with Visual Studio Toolset v141
- For CUDA 9* **and/or** AMD GPUs, CPU:
  - Download version 2 of the precompiled binary from https://github.com/fireice-uk/bittube-miner-dep/releases/download/v2/bittube-miner-dep.zip
  - Version 2 of the pre-compiled dependencies is not compatible with Visual Studio Toolset v140
- Extract archive to `C:\bittube-miner-dep`

### Validate the Dependency Folder

- Open a command line (Windows key + r) and enter `cmd`
- Execute
   ```
   cd c:\bittube-miner-dep
   tree .
   ```
- You should see something like this:
  ```
    C:\bittube-miner-dep>tree .
    Folder PATH listing for volume Windows
    Volume serial number is XX02-XXXX
    C:\XMR-STAK-DEP
    ├───hwloc
    │   ├───include
    │   │   ├───hwloc
    │   │   │   └───autogen
    │   │   └───private
    │   │       └───autogen
    │   └───lib
    ├───libmicrohttpd
    │   ├───include
    │   └───lib
    └───openssl
        ├───bin
        ├───include
        │   └───openssl
        └───lib
  ```

## Compile

- Download bittube-miner [Source Code.zip](https://github.com/fireice-uk/bittube-miner/releases) and save to a location in your home folder (C:\Users\USERNAME\)
- Extract `Source Code.zip` (e.g. to `C:\Users\USERNAME\bittube-miner-<version>`)
- Open a command line (Windows key + r) and enter `cmd`
- Go to extracted source code directory (e.g. `cd C:\Users\USERNAME\bittube-miner-<version>`)
- Execute the following commands (NOTE: path to Visual Studio Community 2017 can be different)
  ```
  # Execute next line only if compiling for Cuda 9.1 and using Visual Studio 2017 >= 15.5 (released 12/04/17)
  "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvarsall.bat" x64 -vcvars_ver=14.11  
  
  "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\Tools\VsMSBuildCmd.bat"
  ```
- Sometimes Windows will change the directory to `C:\Users\USERNAME\source\` instead of `C:\Users\USERNAME\bittube-miner-<version>\`. If that's the case execute `cd C:\Users\USERNAME\bittube-miner-<version>` followed by:
  ```
  mkdir build
  
  cd build
  
  set CMAKE_PREFIX_PATH=C:\bittube-miner-dep\hwloc;C:\bittube-miner-dep\libmicrohttpd;C:\bittube-miner-dep\openssl
  ```

### CMake

- See [build options](https://github.com/fireice-uk/bittube-miner/blob/master/doc/compile.md#build-system) to enable or disable dependencies.
- For CUDA 8* execute: `cmake -G "Visual Studio 15 2017 Win64" -T v140,host=x64 ..`
- For CUDA 9* **and/or** AMD GPUs, CPU execute: `cmake -G "Visual Studio 15 2017 Win64" -T v141,host=x64 ..`
- Then execute
  ```
  cmake --build . --config Release --target install
  
  cd bin\Release
  
  copy C:\bittube-miner-dep\openssl\bin\* .
  ```
- Miner is by default compiled for NVIDIA GPUs (if CUDA is installed), AMD GPUs (if the AMD APP SDK is installed) and CPUs.
