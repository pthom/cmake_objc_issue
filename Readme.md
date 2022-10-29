This repo demonstrates an issue in CMake OBJC support

When two projects that are built are using ObjectiveC, but only one of them mentions `enable_language(OBJC)`, 
then CMake will fail with the message:

````
CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
Missing variable is:
CMAKE_OBJC_COMPILE_OBJECT
CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
Missing variable is:
CMAKE_OBJC_CREATE_STATIC_LIBRARY
````

For example, see https://github.com/pthom/cmake_objc_issue/actions/runs/3352742058/jobs/5555084789


## Tree
This repository is a minimal demonstration repo, with the following content:

````
.
├── .github
│   └── workflows
│       └── build.yml       # A workflow that demonstrates the error on github CI
├── CMakeLists.txt
├── Readme.md
├── lib1
│   ├── CMakeLists.txt
│   └── lib1.m              # unimportant
└── lib2
    ├── CMakeLists.txt
    └── lib2.m              # unimportant
````

## Main CMakeLists:
````cmake
cmake_minimum_required(VERSION 3.11)
project(cmake_objc_issue)

add_subdirectory(lib1)
add_subdirectory(lib2)
````

## lib1/CMakeLists.txt

````cmake
add_library(lib1 lib1.m)
````

## lib2/CMakeLists.txt

````cmake
enable_language(OBJC)
add_library(lib2 lib2.m)
````

## Configure result => bug inside CMake: CMAKE_OBJC_COMPILE_OBJECT not found

````bash
(base) ➜  cmake_objc_issue mkdir build
(base) ➜  cmake_objc_issue cd build
(base) ➜  build cmake ..
-- The C compiler identification is AppleClang 13.1.6.13160021
-- The CXX compiler identification is AppleClang 13.1.6.13160021
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- The OBJC compiler identification is AppleClang 13.1.6.13160021
-- Detecting OBJC compiler ABI info
-- Detecting OBJC compiler ABI info - done
-- Check for working OBJC compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc - skipped
-- Configuring done
CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
Missing variable is:
CMAKE_OBJC_COMPILE_OBJECT
CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
Missing variable is:
CMAKE_OBJC_CREATE_STATIC_LIBRARY
-- Generating done
CMake Generate step failed.  Build files cannot be regenerated correctly.
````