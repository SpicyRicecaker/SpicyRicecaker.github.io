---
layout: "../../layouts/BlogPost.astro"
title: 'A Better C++ Setup for ICPC/Competitive Programming on MacOS'
description: ""
pubDate: "October 22 2023"
# heroImage: "/placeholder-hero.jpg"
---

## Motivation For Not Using the homebrew `gcc` compiler

In my last blog, I talked about how to make the `#include <bits/stdc++.h>` line of code work for `c++`. At the time of posting, I didn't realize that `Xcode 15` had already been released about two weeks prior to that on `September 18, 2023`. Upon installing this update, homebrew's `g++-13` stopped being able to compile anything on MacOS!

The [workaround provided by user Eskimo on Apple Developer](https://developer.apple.com/forums/thread/737707?answerId=765203022#765203022) was adding `-Wl,ld_classic` to the flag, which does fix the error. However, the new linker also came with a couple of other issues, like the annoying 

```shell
ld: warning: duplicate -rpath '/opt/homebrew/lib' ignored
```
warning that as of the time of writing this post, seems to have no widely accepted workarounds.

## Motivation for Using Apple clang version 15.0.0

Apple's new linker in Xcode 15 (known as `ld_prime`) is significantly (by an order of magnitude) faster than the old linker. So much so that now it is around [60% as fast as the mold linker](https://twitter.com/nabbisen/status/1665936938837344256), the fastest linker on the planet as of right now.

This leads to significant gains in build times, especially if you're compiling often trying to debug code.

Furthermore, this increase in linker speed, or rather just the compatability of clang with the rest of the system, leads to much more responsive diagnostics in VSCode, which makes the coding experience all around better.

## How To

The general idea behind this is to 
1. use the same `stdc++.h` file as provided in the official `gcc` repository, slightly touching it up to make sure that it is compatible with Apple's `clang` compiler, then 
2. slapping it in into a path that Apple's `clang` compiler scans when it looks for files `#include`d with `<>` angle brackets.

### Step 1

Let's assume you want your programming scratch source file to be `main.cpp`.

We again go the official [gcc repository](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/precompiled/stdc%2B%2B.h) and search for the `stdc++.h` file. We copy the file into a local `.h` file in the same directory as our `map.cpp` file. We update the include statement in our `main.cpp` file to `#include "stdc++.h"` instead of `#include <bits/stdc++.h>`.

Now we try to compile the `main.cpp` file, and resolve all compiler warnings by removing parts unsupported by the Apple `clang` compiler out of the `stdc++.h` file.

As of Apple's Clang 15, this consists of removing all the `#IF` guards, as well as some 3-4 libraries that are unsupported (don't worry, these libraries are extremely new in the c++ standard and seldom used for CP).

See [my version](https://github.com/SpicyRicecaker/codeforces-template/blob/main/stdc%2B%2B.h) here if you don't feel like making your own `stdc++.h`.

Now, the next step is to move the file into the include path of the apple `clang` compiler. You could theoretically just keep it in the same directory as your `main.cpp` file, but then you'd have to use `#include ""` with quotes instead of angle brackets, making submitting the code significantly harder. So we'll put it in the include paths instead, where `clang` searches by default.

How do we find these include path directories that Clang searches for? We simply run the following command:

```shell
echo | clang -E -v -x c++ -
```

look for any line that starts after the 
```txt
#include <...> search starts here:
```
line.

The path that I use is `/usr/local/include`, since it's the shortest and most readable.

Now we simply move our file over (making sure to create the `bits` directory, if it doesn't exist:)

```shell
sudo mkdir /usr/local/include/bits
# assuming your .h file is in the current directory
sudo mv ./stdc++.h /usr/local/include/bits/stdc++.h 
```

Now you should be able to compile your `main.cpp` with Apple `clang`!

## VSCode

I use the following VSCode settings:

```json
"C_Cpp.default.compilerPath": "clang++",
"C_Cpp.default.intelliSenseMode": "macos-clang-arm64",
"C_Cpp.default.cppStandard": "c++20",
```

The top two lines aren't required, but setting the c++ standard is pretty important. As of October 2023, [ICPC supports up to c++20](https://docs.icpc.global/worldfinals-programming-environment/#heading-3), so it's beneficial to use the latest standard.

---

Thanks all! Thanks for reading. If you have any questions, feel free to reach out to my email at andy.shengdong.li@gmail.com. 