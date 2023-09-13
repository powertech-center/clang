# [C] Cross Compilation
>Build your applications from any platform to any platform. Supported platforms: Windows, macOS, Linux, Android, iOS, FreeBSD, etc._

*Please note that the compiler is under active development, we will update this article as new functionality becomes available. We will be glad for your participation in the development, testing and voicing of proposals. Feel free in [Issues](https://github.com/powertech-center/clang/issues), [Discussions](https://github.com/powertech-center/clang/discussions) or in the telegram group [PowerTech C/C++ Beta](https://t.me/powercpp_beta).*

The Clang compiler initially has a wide range of functionality for [implementing cross compilation](https://clang.llvm.org/docs/CrossCompilation.html), it is enough to specify the `-target` option, which will list the architecture, operating system, calling convention, etc. Our fork supports the most popular architectures: x86, x64, ARM, ARM64, WebAssembly, XCore and AVR. You can always download a suitable set of header files and libraries involved in the compilation, but we took care of you and provide the necessary files for 6 platforms as a base: Windows x86, Windows x64, Linux x64, Linux ARM64, macOS x64 and macOS ARM64.

### Targets (toolchains)
The Clang compiler, like GCC, in the case of cross compilation, looks for a suitable set of header files and libraries, which is called a toolchain. The convenience of our fork is that in addition to the standard toolchain paths, they are searched for in the **./targets** folder of the fork. By default, there are already several toolchains for your targets.

### Advanced configuration
Using our fork, you can easily customize your own targets by modifying the **config.json** file. As an example, you can look at the already described targets `"windows"`, `"linux"`, `"macos"`, etc. The following fields are supported:
* `parent` - required name of the parent target, the parent can be a target from the configuration file.
* `path` - optional field, similar to `--sysroot`, which allows you to explicitly specify the path to the toolchain. An absolute path or a relative path can be used, by default it is searched in the **./targets** folder.
* `args` - an optional field that allows you to add an arbitrary set of compiler options when using this target.
```javascript
{
  ...
  "targets": {
    "windows-x86": {
      "parent": "i386-w64-mingw32"
    },
    "windows-x64": {
      "parent": "x86_64-w64-mingw32"
    },
    "windows-32": {
      "parent": "windows-x86"
    },
    "windows-64": {
      "parent": "windows-x64"
    },
    "linux-x64": {
      "parent": "x86_64-linux-gnu"
    },
    "linux-arm64": {
      "parent": "aarch64-linux-gnu",
      "args": "-static"
    },
    "x86_64-apple-darwin21": {
      "path": "MacOSX13.1.sdk.Lite"
    },
    "arm64-apple-darwin21": {
      "path": "MacOSX13.1.sdk.Lite"
    },
    "macos-x64": {
      "parent": "x86_64-apple-darwin21"
    },
    "macos-arm64": {
      "parent": "arm64-apple-darwin21"
    },
    "windows": {
      "parent": "windows-x64"
    },
    "linux": {
      "parent": "linux-x64"
    },
    "macos": {
      "parent": "macos-arm64"
    },
    "default": {
      "parent": "windows-x64" // depends on the current platform
    }
  }
}
```
Please note that the built-in toolchain for macOS comes in a Lite version - to reduce the size of the release, the `macOS-SDK/System` directory is deleted. But you can use and customize any SDK.

### CMake toolchain files
The very popular CMake build system [allows cross-compilation](https://cmake.org/cmake/help/book/mastering-cmake/chapter/Cross%20Compiling%20With%20CMake.html), for this you should pass the path to the [toolchain file](https://cmake.org/cmake/help/latest/manual/cmake-toolchains.7.html), for example, like this:
```sh
cmake -DCMAKE_TOOLCHAIN_FILE=<path_to_toolchain_file> ...
```
Toolchain files allow you to initialize such important variables as `CMAKE_SYSTEM_NAME`, `CMAKE_SYSTEM_PROCESSOR`, etc., define `-target` and other important build parameters.

Our fork comes with a set of ready-made CMake toolchain files in the `./cmake/toolchains` directory. You will find there files like `macos-arm64.cmake`, `linux-x64.cmake`, `windows-32.cmake`, `x86_64-apple-darwin21.cmake` etc.