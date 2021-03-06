cmake_minimum_required(VERSION 3.1)
project(MTXCLIENT_DEPS)

# Point CMake at any custom modules we may ship
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

set(DEPS_INSTALL_DIR "${CMAKE_BINARY_DIR}/usr"
    CACHE PATH "Dependencies install directory.")
set(DEPS_BIN_DIR "${DEPS_INSTALL_DIR}/bin"
    CACHE PATH "Dependencies binary install directory.")
set(DEPS_LIB_DIR "${DEPS_INSTALL_DIR}/lib"
    CACHE PATH "Dependencies library install directory.")
set(DEPS_BUILD_DIR "${CMAKE_BINARY_DIR}/build"
    CACHE PATH "Dependencies build directory.")
set(DEPS_DOWNLOAD_DIR "${DEPS_BUILD_DIR}/downloads"
    CACHE PATH "Dependencies download directory.")

option(USE_BUNDLED "Use bundled dependencies." ON)

option(USE_BUNDLED_BOOST "Use the bundled version of Boost." ${USE_BUNDLED})
option(USE_BUNDLED_SPDLOG "Use the bundled version of spdlog." ${USE_BUNDLED})
option(USE_BUNDLED_OLM "Use the bundled version of libolm." ${USE_BUNDLED})
option(USE_BUNDLED_GTEST "Use the bundled version of Google Test."
       ${USE_BUNDLED})

option(USE_EXISTING_SRC_DIR
       "Skip download of deps sources in case of existing source directory."
       OFF)

include(ExternalProject)

set(BOOST_URL
    https://dl.bintray.com/boostorg/release/1.68.0/source/boost_1_68_0.tar.bz2)
set(BOOST_SHA256
    7f6130bc3cf65f56a618888ce9d5ea704fa10b462be126ad053e80e553d6d8b7)

set(GTEST_URL https://github.com/google/googletest/archive/release-1.8.0.tar.gz)
set(GTEST_SHA1 e7e646a6204638fe8e87e165292b8dd9cd4c36ed)

set(OLM_URL https://git.matrix.org/git/olm.git)
set(OLM_TAG 4065c8e11a33ba41133a086ed3de4da94dcb6bae)

set(SPDLOG_URL https://github.com/gabime/spdlog/archive/v1.1.0.tar.gz)
set(SPDLOG_HASH
    3dbcbfd8c07e25f5e0d662b194d3a7772ef214358c49ada23c044c4747ce8b19)

set(JSON_HEADER_URL
    https://github.com/nlohmann/json/releases/download/v3.2.0/json.hpp)
set(JSON_HEADER_HASH
    ce6b5610a051ec6795fa11c33854abebb086f0fd67c311f5921c3c07f9531b44)

file(DOWNLOAD ${JSON_HEADER_URL} ${DEPS_INSTALL_DIR}/include/json.hpp
     EXPECTED_HASH SHA256=${JSON_HEADER_HASH})

if(USE_BUNDLED_BOOST)
  include(Boost)
endif()

if(USE_BUNDLED_SPDLOG)
  include(SpdLog)
endif()

if(USE_BUNDLED_OLM)
  include(Olm)
endif()

if(USE_BUNDLED_GTEST)
  include(GoogleTest)
endif()

if(WIN32)
  if("${TARGET_ARCH}" STREQUAL "X86_64")
    set(TARGET_ARCH x64)
  elseif(TARGET_ARCH STREQUAL "X86")
    set(TARGET_ARCH ia32)
  endif()
endif()

add_custom_target(third-party ALL
                  COMMAND ${CMAKE_COMMAND} -E touch .third-party
                  DEPENDS ${THIRD_PARTY_DEPS})
