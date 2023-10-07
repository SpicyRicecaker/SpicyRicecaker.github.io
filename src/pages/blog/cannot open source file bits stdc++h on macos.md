---
layout: "../../layouts/BlogPost.astro"
title: 'Fixing the `cannot open source file "bits/stdc++.h" C/C++ (1696)` error for VSCode on MacOS'
description: ""
pubDate: "October 6 2023"
# heroImage: "/placeholder-hero.jpg"
---

This is a really annoying error that has plagued me for years. I could not find a single solution to resolve this error searching on the entire internet. It's also really surprising as well, does nobody competitively program on MacOS?

However, as of October 6th, 2023, (with the help of ChatGPT-4), I've finally come to a solution which I believe to be optimal. 

It boils down to the following steps:
1) Install `gcc` via `homebrew`
2) Download `stdc++.h` from the official `gcc` repository and put it into the include path of the homebrew installed `gcc`
3) Change the `c++` compiler of the vscode `C/C++` extension to the homebrew installed `g++`.

# Installing `gcc` via `homebrew`

I'll assume you already have `homebrew` installed. If not, get it at https://brew.sh/.

Then, open a terminal, and run the following command:

```shell
brew install gcc
```

# Finding the "Include Path" for the Homebrew installed `gcc`

Run the following command 

```shell
brew list gcc
```

Now look for the line with the following format:

```shell
/opt/homebrew/Cellar/gcc/X.Y.Z/include/c++/
```

Note the `X` `Y` and `Z` values and copy the directory path.

Now `cd` into that directory, then the major version (`X`) subdirectory, then the `bits` subdirectory, or in one command as follows:

```shell
cd /opt/homebrew/Cellar/gcc/[X.Y.Z]/include/c++/[X]/bits # X Y Z may vary
```

Once inside the above directory, create the `stdc++.h` file.

```shell
touch stdc++.h
nano stdc++.h # or open the file with your favorite text editor
```

Now, the `gcc` we installed via homebrew does not include the official `stdc++.h` file by default. To get the official version of this file, we have to go to the official `gcc` repository and copy the contents of that file into our text editor (or download it, but I find copying easier). 

Goto https://github.com/gcc-mirror/gcc, press `t` to search files, then type `stdc++.h` and click on the first file that shows up. I've included the [link to this file as of October 6th 2023](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/include/precompiled/stdc%2B%2B.h), but that file *will* change in the future so best to find it for yourself. 

I've also include the contents of the file below:
(found by searching for `stdc++.h` in the `gcc-mirror/gcc` repository on github) 

```c++
// C++ includes used for precompiling -*- C++ -*-

// Copyright (C) 2003-2023 Free Software Foundation, Inc.
//
// This file is part of the GNU ISO C++ Library.  This library is free
// software; you can redistribute it and/or modify it under the
// terms of the GNU General Public License as published by the
// Free Software Foundation; either version 3, or (at your option)
// any later version.

// This library is distributed in the hope that it will be useful,
// but WITHOUT ANY WARRANTY; without even the implied warranty of
// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
// GNU General Public License for more details.

// Under Section 7 of GPL version 3, you are granted additional
// permissions described in the GCC Runtime Library Exception, version
// 3.1, as published by the Free Software Foundation.

// You should have received a copy of the GNU General Public License and
// a copy of the GCC Runtime Library Exception along with this program;
// see the files COPYING3 and COPYING.RUNTIME respectively.  If not, see
// <http://www.gnu.org/licenses/>.

/** @file stdc++.h
 *  This is an implementation file for a precompiled header.
 */

// 17.4.1.2 Headers

// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <csetjmp>
#include <cstdarg>
#include <cstddef>
#include <cstdlib>

#if __cplusplus >= 201103L
#include <cstdint>
#endif

// C++
// #include <bitset>
// #include <complex>
#include <algorithm>
#include <bitset>
#include <functional>
#include <iterator>
#include <limits>
#include <memory>
#include <new>
#include <numeric>
#include <typeinfo>
#include <utility>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <initializer_list>
#include <ratio>
#include <scoped_allocator>
#include <tuple>
#include <typeindex>
#include <type_traits>
#endif

#if __cplusplus >= 201402L
#endif

#if __cplusplus >= 201703L
#include <any>
// #include <execution>
#include <optional>
#include <variant>
#include <string_view>
#endif

#if __cplusplus >= 202002L
#include <bit>
#include <compare>
#include <concepts>
#include <numbers>
#include <ranges>
#include <span>
#include <source_location>
#include <version>
#endif

#if __cplusplus > 202002L
#include <expected>
#include <stdatomic.h>
#if __cpp_impl_coroutine
# include <coroutine>
#endif
#endif

#if _GLIBCXX_HOSTED
// C
#ifndef _GLIBCXX_NO_ASSERT
#include <cassert>
#endif
#include <cctype>
#include <cerrno>
#include <cfloat>
#include <ciso646>
#include <climits>
#include <clocale>
#include <cmath>
#include <csetjmp>
#include <csignal>
#include <cstdarg>
#include <cstddef>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
#include <cwchar>
#include <cwctype>

#if __cplusplus >= 201103L
#include <ccomplex>
#include <cfenv>
#include <cinttypes>
#include <cstdalign>
#include <cstdbool>
#include <cstdint>
#include <ctgmath>
#include <cuchar>
#endif

// C++
#include <complex>
#include <deque>
#include <exception>
#include <fstream>
#include <functional>
#include <iomanip>
#include <ios>
#include <iosfwd>
#include <iostream>
#include <istream>
#include <iterator>
#include <limits>
#include <list>
#include <locale>
#include <map>
#include <memory>
#include <new>
#include <numeric>
#include <ostream>
#include <queue>
#include <set>
#include <sstream>
#include <stack>
#include <stdexcept>
#include <streambuf>
#include <string>
#include <typeinfo>
#include <utility>
#include <valarray>
#include <vector>

#if __cplusplus >= 201103L
#include <array>
#include <atomic>
#include <chrono>
#include <codecvt>
#include <condition_variable>
#include <forward_list>
#include <future>
#include <initializer_list>
#include <mutex>
#include <random>
#include <ratio>
#include <regex>
#include <scoped_allocator>
#include <system_error>
#include <thread>
#include <tuple>
#include <typeindex>
#include <type_traits>
#include <unordered_map>
#include <unordered_set>
#endif

#if __cplusplus >= 201402L
#include <shared_mutex>
#endif

#if __cplusplus >= 201703L
#include <any>
#include <charconv>
// #include <execution>
#include <filesystem>
#include <optional>
#include <memory_resource>
#include <variant>
#endif

#if __cplusplus >= 202002L
#include <barrier>
#include <bit>
#include <compare>
#include <concepts>
#include <format>
#include <latch>
#include <numbers>
#include <ranges>
#include <span>
#include <stop_token>
#include <semaphore>
#include <source_location>
#include <syncstream>
#include <version>
#endif

#if __cplusplus > 202002L
#include <expected>
#include <spanstream>
#if __has_include(<stacktrace>)
# include <stacktrace>
#endif
#include <stdatomic.h>
#include <stdfloat>
#endif

#endif // HOSTED
```

Copy the text of the `stdc++.h` file, from github or from this blog above. 

Now paste into the `nano` editor (or your editor of choice) by pressing `control + shift + v` at the same time. Then press `control + x` and `y` to accept saving the file.

# Make VSCode use the homebrew-installed `g++`

First, find the path of the binary. Open a terminal and type

```shell
brew list
```

again. Find the line that looks like 

```shell
/opt/homebrew/Cellar/gcc/X.Y.Z/bin/g++-X
```

and note the `X` version down.

Now open VSCode. Press `command + shift + p` at the same time. Type in `Preferences: Open User Settings (JSON)` and press `enter`.

Before the last JSON `}` bracket, add the following information (where `XYZ` is *your* gcc version):

```json
"C_Cpp.default.compilerPath": "g++-X",
```

Save and close the file.

Now close and open vscode again. Intellisense should now be working, and so should running your cpp program inside VSCode with `#include <bits/stdc++.h>`. Congrats!

# Addendum: Running From the Terminal

If you want to compile your single file cpp program inside the terminal and run it, simply make sure you're in the same directory as your file and type the following

```shell
g++-13 [yourfile.cpp] && ./a.out
```

Profit!

If you have any questions you can email me at awesomenessisgolden@gmail.com and I'd be happy to try and help you.