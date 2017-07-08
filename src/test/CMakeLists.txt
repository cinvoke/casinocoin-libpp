# cmake file for casinocoinlibppdemo

list(APPEND CMAKE_MODULE_PATH
  "${CMAKE_CURRENT_SOURCE_DIR}/../../extras/casinocoind/Builds/CMake")
include(CMakeFuncs)

set(openssl_min 1.0.2)

parse_target()

if (nonunity)
  set(target "" CACHE STRING "Build target. Nounity not supported" FORCE)
  message(FATAL_ERROR "Nonunity builds not supported")
endif()

setup_build_cache()

############################################################

determine_build_type()

check_gcc4_abi()

############################################################

include_directories(
  SYSTEM
  ../../extras/casinocoind/src
  ../../extras/casinocoind/src/beast/include
  ../../extras/casinocoind/src/beast/extras
  ../../src/include)

special_build_flags()

############################################################

use_boost(
  regex
  system
  thread)

use_pthread()

use_openssl(${openssl_min})

setup_build_boilerplate()

############################################################

set(demo_src casinocoin-libpp_demo.cpp)

############################################################

if (WIN32 OR is_xcode)
  file(GLOB_RECURSE all_headers
    src/*.hpp
    src/*.h
    ../../extras/casinocoind/src/*.hpp
    ../../extras/casinocoind/src/*.h
    ../../extras/*/CMakeFuncs.cmake)
  list(APPEND demo_src "${all_headers}")

  # Properties
  set_property(
    SOURCE ${all_headers}
    APPEND
    PROPERTY HEADER_FILE_ONLY
    true)
endif()

if (NOT is_msvc)
  set(no_unused_w -Wno-unused-function)
else()
  unset(no_unused_w)
endif()

############################################################

if (WIN32 OR is_xcode)
  group_sources(src)
  group_sources(extras/casinocoind/src)
  group_sources(extras/casinocoind/Builds)
endif()

############################################################

set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

add_executable(casinocoinlibppdemo
    ${demo_src}
    $<TARGET_OBJECTS:casinocoinlibpp>)

set_startup_project(casinocoinlibppdemo)

target_link_libraries(casinocoinlibppdemo
    ${OPENSSL_LIBRARIES}
    ${SANITIZER_LIBRARIES})

link_common_libraries(casinocoinlibppdemo)
