# [C] Cross Compilation
>Build your applications from any platform to any platform. Supported platforms: Windows, macOS, Linux, Android, iOS, FreeBSD, etc._

*Please note that the compiler is under active development, we will update this article as new functionality becomes available. We will be glad for your participation in the development, testing and voicing of proposals. Feel free in [Issues](https://github.com/powertech-center/clang/issues), [Discussions](https://github.com/powertech-center/clang/discussions) or in the telegram group [PowerTech C/C++ Beta](https://t.me/powercpp_beta).*

The Clang compiler initially has a wide range of functionality for implementing cross compilation, it is enough to specify the `-target` option, which will list the architecture, operating system, calling convention, etc. Our fork supports the most popular architectures: x86, x64, ARM, ARM64, WebAssembly, XCore and AVR. You can always download a suitable set of header files and libraries involved in the compilation, but we took care of you and provide the necessary files for 6 platforms as a base: Windows x86, Windows x64, Linux x86, Linux x64, macOS x64 and macOS ARM64.

### Targets (toolchains)
The Clang compiler, like GCC, in the case of cross compilation, looks for a suitable set of header files and libraries, which is called a toolchain. The convenience of our fork is that in addition to the standard toolchain paths, they are searched for in the **./targets** folder of the fork. By default, there are already several toolchains for your targets.

### Advanced configuration
The **config.json** configuration file allows you to make it as easy as possible to configure your own targets. For example, thanks to the example below, you can freely use targets such as `"windows"`, `"linux"` or `"macos"`. The following fields are supported:
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
		"linux-x86": {
			"parent": "i386-linux-gnu"
		},
		"linux-x64": {
			"parent": "x86_64-linux-gnu"
		},
		"darwin-x64": {
			"parent": "x86_64-apple-darwin",
			"path": "MacOSX13.sdk"
		},
		"darwin-arm64": {
			"parent": "arm64-apple-darwin",
			"path": "MacOSX13.sdk"
		},
		"windows": {
			"parent": "windows-x64"
		},
		"linux": {
			"parent": "linux-x64"
		},
		"macos": {
			"parent": "darwin-arm64"
		},
		"default": {
			"parent": "windows-x64"
		}
	}
}
```