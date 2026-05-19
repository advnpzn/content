---
title: vcpkg - A C++ Package Manager
date: 2024-03-30
description: 'Learn how to use vcpkg in manifest mode to manage C++ dependencies easily and efficiently.'
tags:
  - C++
  - vcpkg
  - Package Manager
  - CMake
  - Tutorial
---

Do you use C++?

Do you struggle with maintaining dependencies for your projects?

Is it taking too much time to setup your C++ development environment setup?

If YES, then consider that your development workflow is going to become smooth after reading this post.

One of the things where we bang our heads when dealing with C++ is managing third party dependencies that we use in our projects. Be it a seasoned user or a novice, everyone must have tackled this issue somewhere at sometime using different ways.

![Struggle with C++ dependencies](./assets/struggle.png)

But is there a thing as which one's the "best" way?

Of course, there isn't, but anything that makes me not bang my head against the table, I would consider it a better way. And this is where we'll be talking about Package Managers, and specifically about `vcpkg`.

## vcpkg

[vcpkg](https://vcpkg.io/en/) is a package or dependency manager for C++. It's very unfortunate that there is no official package manager for C++ at its dawn. I am not sure if the concept of "package managers" even existed.

## Installation

It's pretty easy actually.

We'll be focusing on GNU/Linux installation entirely. This applies for usage as well. I'll be using Debian 12. But 90% of the time, it's pretty much the same in Microsoft Windows.

Clone the repo.

```bash
git clone https://github.com/microsoft/vcpkg.git
```

Run the script

```bash
cd vcpkg && ./bootstrap-vcpkg.sh
```

You can add `-disableMetrics` as an argument to the shell script, if you don't want Microsoft to spy on you.

Then if you want to run `vcpkg` from anywhere in your shell, you can add the PATH to your `.bashrc` or profile file.

```bash
export VCPKG_ROOT=/path/to/vcpkg
export PATH=$VCPKG_ROOT:$PATH
```

After this, you can probably run `vcpkg` from any directory.

## Usage

Now to come to the part where we actually use `vcpkg` in our C++ project. I'll use a demo project.

## Demo Project

We will be using [fmt](https://github.com/fmtlib/fmt) library to print something.

`main.cxx`

```cpp
#include <fmt/core.h>

int main() {
  fmt::print("Mitochondria is the powerhouse of the cell!\n");
  
  return 0;
}
```

And to build this project, we'll be using CMake.

So, our initial project directory would look like this.

```
demo/
├── CMakeLists.txt
└── main.cxx
```

Now from our `demo` directory, do

```bash
vcpkg new --application
```

This would create two new files in our `demo` directory.

- `vcpkg.json` - The file where we define our dependencies.
- `vcpkg-configuration.json` - The file where we define how `vcpkg` behaves, such as which custom registry to use etc.

The above way of initialising & using `vcpkg` is called as **Manifest Mode**.

There are 2 modes generally:

- Classic
- Manifest

We'll be using Manifest mode throughout this post, because it is BETTER!

Now, to add a dependency to our project, which is `fmt`:

```bash
vcpkg add port fmt
```

You can search for many packages at [https://vcpkg.io/en/packages](https://vcpkg.io/en/packages).

Now, if you open that `vcpkg.json` file, you can see that the dependency we added is present.

```json
{
  "dependencies": [
    "fmt"
  ]
}
```

## CMakeLists.txt

Make sure you have `cmake` installed beforehand along with something like `make` or `ninja`. Since this is a small demo project, we can have the following in our demo project.

`CMakeLists.txt`

```cmake
cmake_minimum_required(VERSION 3.12)

project("demo")

find_package(fmt CONFIG REQUIRED)

add_executable(demo "main.cxx")
target_link_libraries(demo PRIVATE fmt::fmt)
```

Now, obviously, when you run this:

```bash
cmake -S . -B build
```

you'll be getting an error.

Now to use vcpkg with CMake, we need CMake to use the vcpkg CMake toolchain file (`vcpkg.cmake`) when we configure our CMake against our `CMakeLists.txt` file.

We can do this by specifying the path to the toolchain file using `CMAKE_TOOLCHAIN_FILE` to pass the path to the `vcpkg.cmake` file. This can be done in many ways. But for now, we will use this as an argument with the CMake command.

Delete the existing `build` directory. Then, do

```bash
cmake -S . -B build -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake
```

Now this would actually use `vcpkg` to build the dependencies specified, and at the end it also shows how to utilize the dependency in your `CMakeLists.txt` file.

```
The package fmt provides CMake targets:

    find_package(fmt CONFIG REQUIRED)
    target_link_libraries(main PRIVATE fmt::fmt)

    # Or use the header-only version
    find_package(fmt CONFIG REQUIRED)
    target_link_libraries(main PRIVATE fmt::fmt-header-only)
```

After this configuration, we can try building.

```bash
cmake --build build
```

This above command would probably output the following:

```
-- Running vcpkg install
Detecting compiler hash for triplet x64-linux...
Compiler found: /usr/bin/c++
All requested packages are currently installed.
Total install time: 1.12 us
The package fmt provides CMake targets:

    find_package(fmt CONFIG REQUIRED)
    target_link_libraries(main PRIVATE fmt::fmt)

    # Or use the header-only version
    find_package(fmt CONFIG REQUIRED)
    target_link_libraries(main PRIVATE fmt::fmt-header-only)

-- Running vcpkg install - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/mito/demo/build
[ 50%] Building CXX object CMakeFiles/demo.dir/main.cxx.o
[100%] Linking CXX executable demo
[100%] Built target demo
```

Looks like the binary `demo` was built. Let's try running it.

```bash
./build/demo
```

```
Mitochondria is the powerhouse of the cell!
```

It works!

Now this is far better than manually downloading the source code of the library, building it, using the header files in your src, etc..
