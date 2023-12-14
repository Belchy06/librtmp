# librtmp 

The `librtmp` library from `git://git.ffmpeg.org/rtmpdump` modified to compile on Windows with MSVC.

## How to build

1. `mkdir build`
2. `cd build`
3. `cmake -G "Visual Studio 17 2022" -A x64 ..`
4. `cmake --build . --target librtmp`

## TODO:
- Accept custom paths to OpenSSL and zlib
