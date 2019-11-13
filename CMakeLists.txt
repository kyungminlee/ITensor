cmake_minimum_required(VERSION 3.10)
project(itensor
    LANGUAGES CXX
    VERSION 3.0
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON) # for DLL
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

option(BUILD_SHARED "Build shared library" OFF)

if(EXISTS "${CMAKE_SOURCE_DIR}/Config.cmake")
    include("${CMAKE_SOURCE_DIR}/Config.cmake")
endif()

if(MSVC)
    add_compile_options("/bigobj")
    add_definitions(-Dnot=! -Dand=&&)
    add_definitions(-D_SILENCE_CXX17_RESULT_OF_DEPRECATION_WARNING)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    add_definitions(-D_SILENCE_CXX17_SHARED_PTR_UNIQUE_DEPRECATION_WARNING)
endif()

list(APPEND SOURCES
    itensor/util/args.cc
    itensor/util/input.cc
    itensor/util/cputime.cc
    itensor/tensor/lapack_wrap.cc
    itensor/tensor/vec.cc
    itensor/tensor/mat.cc
    itensor/tensor/gemm.cc
    itensor/tensor/algs.cc
    itensor/tensor/contract.cc
    itensor/itdata/dense.cc
    itensor/itdata/combiner.cc
    itensor/itdata/diag.cc
    itensor/itdata/qdense.cc
    itensor/itdata/qcombiner.cc
    itensor/itdata/qdiag.cc
    itensor/itdata/scalar.cc
    itensor/qn.cc
    itensor/tagset.cc
    itensor/index.cc
    itensor/indexset.cc
    itensor/itensor.cc
    itensor/spectrum.cc
    itensor/decomp.cc
    itensor/hermitian.cc
    itensor/svd.cc
    itensor/global.cc
    itensor/mps/mps.cc
    itensor/mps/mpsalgs.cc
    itensor/mps/mpo.cc
    itensor/mps/mpoalgs.cc
    itensor/mps/autompo.cc
)

if (BUILD_SHARED)
    add_library(itensor SHARED ${SOURCES})
else()
    add_library(itensor STATIC ${SOURCES})
endif()

target_include_directories(itensor PUBLIC ${CMAKE_SOURCE_DIR})
target_include_directories(itensor PUBLIC ${LAPACK_INCLUDE_DIR})

if (BUILD_SHARED)
    target_link_libraries(itensor PUBLIC ${LAPACK_LIBRARIES})
endif()

if(NOT EXISTS "${CMAKE_SOURCE_DIR}\\itensor\\config.h")
    file(WRITE "${CMAKE_SOURCE_DIR}\\itensor\\config.h" "#pragma once
#ifndef PLATFORM_${PLATFORM}
#define PLATFORM_${PLATFORM}
#endif
")
endif()
