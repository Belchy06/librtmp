set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(LIBRTMP_SOURCES
    "amf.c"
    "amf.h"
    "bytes.h"
    "dh.h"
    "dhgroups.h"
    "handshake.h"
    "hashswf.c"
    "http.h"
    "log.c"
    "log.h"
    "parseurl.c"
    "rtmp_sys.h"
    "rtmp.c"
    "rtmp.h")

if(MSVC)
  set(cxx_base_flags /GS /W4 /WX)
  set(cxx_exception_flags "-EHsc")
  set(cxx_strict_flags
    /wd4244 # disabled:
    /wd4996 # disabled: 'sprintf': This function or variable may be unsafe. Consider using sprintf_s instead.
    /wd4100 # disabled: unreferenced formal parameter
    /wd4267 # disabled: conversion from 'size_t' to 'int', possible loss of data
    /wd4706 # disabled: assignment within conditional expression
    /wd4018 # disabled: '>': signed/unsigned mismatch
  )
elseif(CMAKE_COMPILER_IS_GNUCXX)
  set(cxx_base_flags -Wall -Wshadow)
  set(cxx_exception_flags "-fexceptions")
  set(cxx_strict_flags -Werror -Wextra -Wunused -Wold-style-cast -Wlogical-op -Wpointer-arith
        -Wno-unused-parameter -Wno-missing-field-initializers)
  if(OVC_TARGET_ARCH STREQUAL "x86" AND MINGW)
    list(APPEND cxx_base_flags "-mxsave")
  endif()
  if(CMAKE_CXX_COMPILER_VERSION VERSION_LESS 4.8)
    message(FATAL_ERROR "thread_local requires gcc >= 4.8")
    set(cxx_strict_flags ${cxx_strict_flags} -Wno-error=missing-braces)
  endif()
elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  set(cxx_base_flags -Wall -Wshadow)
  set(cxx_exception_flags "-fexceptions")
  set(cxx_strict_flags -Werror -Wextra -Wno-missing-braces -Wno-unused-const-variable -Wno-unused-parameter)
  if(OVC_TARGET_ARCH STREQUAL "x86" AND MINGW)
    list(APPEND cxx_base_flags "-mxsave")
  endif()
endif()
set(cxx_default ${cxx_base_flags} ${cxx_exception_flags})
set(cxx_strict ${cxx_strict_flags})
set(linker_flags "")
          
add_library (librtmp OBJECT ${LIBRTMP_SOURCES})
target_compile_options(librtmp PRIVATE ${cxx_default} ${cxx_strict})
target_include_directories(librtmp PUBLIC .)

add_definitions( -DNO_CRYPTO )