# DmGraph

DmGraph is a graph library for C++. It is designed to be simple to use and efficient. It is a header-only library, so you don't need to build it. Just include the header file and you are ready to go.

Origin source is from codeproject.com [D-Graph-ActiveX-Control-in-Cplusplus-with-ATL-No-M](https://www.codeproject.com/Articles/310494/D-Graph-ActiveX-Control-in-Cplusplus-with-ATL-No-M)

## requirements

- C++14
- CMake 3.20 or later

## How to build

cd to the root directory of the project and run the following commands:

```cmd

cmake . -B out\Debug -G "Visual Studio 14 2015" -A Win32 -DCMAKE_BUILD_TYPE="Debug"
cmake --build out\Debug --config Debug

```
