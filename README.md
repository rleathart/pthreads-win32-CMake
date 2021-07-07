Requirements
============

Tested with Clang 12.0.0 and MSVC 19.28.29914.0 on Windows 10.

Building and installing
=======================

First you will need the pthreads-win32 source files. You can download them
[here](https://mirrors.kernel.org/sourceware/pthreads-win32/). You'll want one
of the `pthreads-w32-x-x-x-release.(zip|tar.gz)` archives. This has been tested
with `pthreads-w32-2-9-1-release` but may well work with other versions. After
extracting, the pthreads source files should be in the same directory as the
`CMakeLists.txt` file.

Now you can simply

```bash
cmake -B build -G Ninja -DBUILD_SHARED_LIBS=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build
cmake --install build
```

You can now use pthreads-win32 in other CMake projects by using
```cmake
if(WIN32)
  find_package(pthread REQUIRED)
endif()
```
