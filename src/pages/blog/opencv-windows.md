---
layout: "../../layouts/BlogPost.astro"
title: "Installing OpenCV on Windows for Rust"
description: "Installing OpenCV on Rust is the most medieval, low-level program that I have ever installed in all of history."
pubDate: "Feburary 22 2023"
---

I'm dying to install [Infinite-Storage-Glitch](https://github.com/DvorakDwarf/Infinite-Storage-Glitch) on windows, but unfortunately the program requires an `opencv` dependency to compile.

Because `Infinite-Storage-Glitch` uses the [`opencv-rust`](https://github.com/twistedfall/opencv-rust) library as a dependency, we can go to the [windows package](https://github.com/twistedfall/opencv-rust#windows-package) section of `opencv-rust` to see the windows installation instructions.

`opencv-rust` lists two options to install `opencv`:

- Option 1 is to install it through the chocolatey package manager on Windows. However, chocolatey package maintainers often neglect to update their chocolatey packages, and so chocolatey packages fall behind in versioning. As of writing this article, version `4.7.0` of `opencv` has been out for 3 months, but the package is still not updated. 


- Option 2, is to install `opencv-rust` through `vcpkg`. However, this installation is is next to impossible, because `vcpkg` will recompile the entire windows operating system, leading to long-until-after-you-die-of-old-age compile times. 

And so, Option 3, which I have created, is to directly navigate to https://opencv.org/releases/ and download the latest "windows" `opencv` installer. 

# How to Install the Official OpenCV Compiled Binary to Compile Rust Programs

Run the `.exe` installer. Choose an easy-to-reach download path, like `C:\Users\user\Downloads`.

Now we must set three environment variables.

## 1. Set the Required OpenCV Environment Variables to Compile Rust Programs
- The steps listed here were inspired by https://github.com/twistedfall/opencv-rust/issues/118#issuecomment-619608278 and https://github.com/twistedfall/opencv-rust/issues/113#issue-596076777.

We need to create 3 environment variables:
`OPENCV_INCLUDE_PATHS`, `OPENCV_LINK_LIBS`, and `OPENCV_LINK_PATHS`.

To create an environment variable, open the Windows Search Bar. Type `edit environment variables for your account`, then click `enter`. In the menu, under `User variables for [your username]`, click on `New...`. 

The values for each of the 3 environment variable **as of ver `4.7.0`** are as follows: 

1. `OPENCV_LINK_LIBS=opencv_world470`

2. `OPENCV_LINK_PATHS=C:\Users\user\Downloads\opencv\build\x64\vc16\lib` 

> The location of this path may have changed since `4.7.0`. It should include the `opencv_worldxxx.lib` files, as well as various `.cmake` files.

3. `OPENCV_INCLUDE_PATHS=C:\Users\user\Downloads\opencv\build\include` 

> The location of this path may have changed since `4.7.0`. It should include the `opencv2` folder, and inside the `opencv2` folder, there should be various `.hpp` and `.h` files.

Finally, we will install the `opencv` binary to our runtime path.

## 2. Add OpenCV to `PATH`

As of version `4.7.0`, the path to the `opencv` executables are located at `C:\Users\user\Downloads\opencv\build\x64\vc16\bin`. 

> The location of this path may have changed since `4.7.0`. It should include `opencv_worldxxx.dll` and `opencv_worldxxx.pdb` files, as well as various `.exe` and `.dll` files.

Open the Windows Search Bar. Type `edit environment variables for your account`, then click `enter`. In the menu, under `User variables for [your username]`, click on `Path`. Here, click new, and press `v` while holding `ctrl` to paste in the path we have previously copied.

## 3. Install `clang` for Windows

Unfortunatey, `opencv-rust` still requires us to download and extract the clang compiler and add it to path. The easiest way to download clang is via the `winget` package manager: `winget install llvm`.

Alternatively, goto the [LLVM downloads page](https://releases.llvm.org/download.html). Next, find the latest llvm version. Click on the latest `page` link. Finally, click on the link with the label `LLVM-x.x.x-win64.exe` to download the file. Run the `.exe` and follow the instructions to extract its contents.

After the install, we must add `clang`'s `bin` directory to path. Copy the path `C:\Program Files\LLVM\bin` and add it to `PATH`.

## 4. Now try to compile any opencv project

Open a new terminal window, or if you are using a graphical editor restart the editor and the editor's shell. Navigate to a Rust project that uses `opencv` as a dependency. Then type `cargo run` and hit enter for profit.

> `opencv` for Rust has been the most convoluted program I've ever installed in all my life. Arch Linux install is easier as it's actually documented. There's no way it should be this difficult.