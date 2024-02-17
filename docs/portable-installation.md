# Portable Installation
> Enjoy maximum flexibility when manually installing a portable build.

*Please note that the compiler is under active development, we will update this article as new functionality becomes available. We will be glad for your participation in the development, testing and voicing of proposals. Feel free in [Issues](https://github.com/powertech-center/clang/issues), [Discussions](https://github.com/powertech-center/clang/discussions) or in the telegram group [PowerTech C/C++ Beta](https://t.me/powercpp_beta).*

The idea of the portable version is simple - you just take the archive, unpack it, and use the contents as you want. For example, in the CMake build settings you can explicitly specify the path to the `clang` and `clang++` compilers. You can do this yourself, find the information you need on the Internet. Below are instructions that can simplify downloading, unpacking and creating the necessary symlinks.

### Linux/macOS
First, you need to make sure that you have `curl` and `7z` utilities. If they exist, both paths will be displayed. If at least one path is empty, you need to install them.
```bash
which curl && which 7z
```
When both utilities are installed, you can, for example, use these scripts:
```bash
# variables
url=https://your-clang-archive-url.7z
path=/opt/powerclang
# cleanup directory
sudo rm -rf $path
sudo mkdir $path
# download, extract and remove archive
curl $url --output powerclang.7z
sudo 7z x powerclang.7z "-o$path"
rm powerclang.7z
# install
sudo $path/create_symlinks.sh
```
The `create_symlinks.sh` script, which is included in the portable archive, creates symlinks. But besides this, it can take 2 parameters: the path to the resulting directory and the `-default` parameter, which indicates that it is also necessary to create symlinks for the default compiler - `cc` and `c++`.

### Windows
To unpack a portable archive for Windows, you will need the [7Zip](https://www.7-zip.org) or [WinRAR](https://www.win-rar.com) utility. For convenience, you can unpack the archive into the `C:\Program Files\powerclang` folder, but most likely you will need administrator rights.
![](../images/portable-installation/1.png)
![](../images/portable-installation/2.png)

If you want the compiler and other utilities to be available for the global scope, you will need to add the path to the bin directory to your `PATH` environment variable. Or you can run the `create_env_path.ps1` script from PowerShell. Most likely you will need administrator rights.
![](../images/portable-installation/3.png)