Requirements
============

Tested with Clang 12.0.0 and MSVC 19.28.29914.0 on Windows 10.

Building and installing
=======================

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
