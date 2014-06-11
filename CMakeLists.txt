# Set the makefile build type (only for make file based generators)
MARK_AS_ADVANCED( CMAKE_BUILD_TYPE )
set(CMAKE_BUILD_TYPE Debug CACHE STRING "The build type for makefile based generators")


#The name of the project
PROJECT(ModernGPU)

#The CMake Minimum version that is required. The FindCUDA script
#is distributed since version 2.8
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)


set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules/;${CMAKE_MODULE_PATH}")
message(STATUS "Module path is now set to: " ${CMAKE_MODULE_PATH} )
include(PrintListMacro)

#Add some multithreaded build support
MARK_AS_ADVANCED(MULTITHREADED_BUILD)
set(MULTITHREADED_BUILD 12 CACHE STRING "How many threads are used to build the project")
if(MULTITHREADED_BUILD)
    if(${CMAKE_GENERATOR} MATCHES "Unix Makefiles")
            message(STATUS ${CMAKE_MAKE_PROGRAM})
            #set(CMAKE_MAKE_PROGRAM "${CMAKE_MAKE_PROGRAM} -j${MULTITHREADED_BUILD}")
            message(STATUS "Added arguments to CMAKE_BUILD_TOOL: ${CMAKE_MAKE_PROGRAM}")
    elseif(MSVC)
      set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP")
      message(STATUS "Added parallel build arguments to CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")
    endif()
endif()

# Searching CUDA and Flags ======================================================
# Set the flags
SET(MYPROJECT_CUDA_PROPAGATE_HOST_FLAGS OFF CACHE BOOL "Propagate C/CXX Flags and friends to the host compiler in NVCC via -Xompile")

# Set the architecture for the grafics card!
SET(MYPROJECT_CUDA_NVCC_FLAGS
"-gencode=arch=compute_20,code=\\\"sm_20,compute_20\\\""
"-gencode=arch=compute_30,code=\\\"sm_30,compute_30\\\""
"-gencode=arch=compute_35,code=\\\"sm_35,compute_35\\\""
"--ptxas-options=-v"
CACHE VALUE "Semicolon delimited several arguments for the NVCC compiler")

SET(MYPROJECT_CUDA_NVCC_FLAGS_DEBUG
"-g"
"-G"
CACHE VALUE "Semicolon delimited several arguments for the NVCC compiler")

SET(MYPROJECT_CUDA_NVCC_FLAGS_RELEASE "-O2"
CACHE VALUE "Semicolon delimited several arguments for the NVCC compiler")

FIND_PACKAGE(CUDA REQUIRED)
SET(CUDA_VERBOSE_BUILD ON)
SET(CUDA_NVCC_FLAGS           "${MYPROJECT_CUDA_NVCC_FLAGS}"            CACHE INTERNAL "Internal flags do not edit" FORCE)
SET(CUDA_NVCC_FLAGS_DEBUG     "${MYPROJECT_CUDA_NVCC_FLAGS_DEBUG}"      CACHE INTERNAL "Internal flags do not edit" FORCE)
SET(CUDA_NVCC_FLAGS_RELEASE   "${MYPROJECT_CUDA_NVCC_FLAGS_RELEASE}"    CACHE INTERNAL "Internal flags do not edit" FORCE)
SET(CUDA_PROPAGATE_HOST_FLAGS "${MYPROJECT_CUDA_PROPAGATE_HOST_FLAGS}"  CACHE INTERNAL "Internal flags do not edit" FORCE)


SET(MYPROJECT_CXX_FLAGS "-std=c++11" CACHE STRING "The project's own compiler flags")

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${MYPROJECT_CXX_FLAGS}")
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${MYPROJECT_CXX_FLAGS}")
MESSAGE(STATUS "Added ${MYPROJECT_CXX_FLAGS} to CMAKE_CXX and CMAKE_C_FLAGS: ${CMAKE_CXX_FLAGS} and ${CMAKE_C_FLAGS}")
#=================================================================================

# Set Common Source
SET(COMMON_INC 
${PROJECT_SOURCE_DIR}/include/sparsematrix.h
${PROJECT_SOURCE_DIR}/include/mgpuhost.cuh
${PROJECT_SOURCE_DIR}/include/mgpudevice.cuh
${PROJECT_SOURCE_DIR}/include/moderngpu.cuh
${PROJECT_SOURCE_DIR}/include/device/sortnetwork.cuh
${PROJECT_SOURCE_DIR}/include/device/deviceutil.cuh
${PROJECT_SOURCE_DIR}/include/device/ctaloadbalance.cuh
${PROJECT_SOURCE_DIR}/include/device/serialsets.cuh
${PROJECT_SOURCE_DIR}/include/device/devicetypes.cuh
${PROJECT_SOURCE_DIR}/include/device/ctascan.cuh
${PROJECT_SOURCE_DIR}/include/device/ctasearch.cuh
${PROJECT_SOURCE_DIR}/include/device/ctasegscan.cuh
${PROJECT_SOURCE_DIR}/include/device/intrinsics.cuh
${PROJECT_SOURCE_DIR}/include/device/loadstore.cuh
${PROJECT_SOURCE_DIR}/include/device/launchbox.cuh
${PROJECT_SOURCE_DIR}/include/device/ctasortedsearch.cuh
${PROJECT_SOURCE_DIR}/include/device/ctasegreduce.cuh
${PROJECT_SOURCE_DIR}/include/device/ctamerge.cuh
${PROJECT_SOURCE_DIR}/include/device/ctasegsort.cuh
${PROJECT_SOURCE_DIR}/include/kernels/spmvcsr.cuh
${PROJECT_SOURCE_DIR}/include/kernels/segreducecsr.cuh
${PROJECT_SOURCE_DIR}/include/kernels/scan.cuh
${PROJECT_SOURCE_DIR}/include/kernels/reducebykey.cuh
${PROJECT_SOURCE_DIR}/include/kernels/merge.cuh
${PROJECT_SOURCE_DIR}/include/kernels/reduce.cuh
${PROJECT_SOURCE_DIR}/include/kernels/csrtools.cuh
${PROJECT_SOURCE_DIR}/include/kernels/loadbalance.cuh
${PROJECT_SOURCE_DIR}/include/kernels/segreduce.cuh
${PROJECT_SOURCE_DIR}/include/kernels/segmentedsort.cuh
${PROJECT_SOURCE_DIR}/include/kernels/join.cuh
${PROJECT_SOURCE_DIR}/include/kernels/localitysort.cuh
${PROJECT_SOURCE_DIR}/include/kernels/mergesort.cuh
${PROJECT_SOURCE_DIR}/include/kernels/sortedsearch.cuh
${PROJECT_SOURCE_DIR}/include/kernels/cubradixsort.cuh
${PROJECT_SOURCE_DIR}/include/kernels/sets.cuh
${PROJECT_SOURCE_DIR}/include/kernels/bulkremove.cuh
${PROJECT_SOURCE_DIR}/include/kernels/intervalmove.cuh
${PROJECT_SOURCE_DIR}/include/kernels/bulkinsert.cuh
${PROJECT_SOURCE_DIR}/include/kernels/search.cuh
${PROJECT_SOURCE_DIR}/include/mmio.h
${PROJECT_SOURCE_DIR}/include/mgpuenums.h
${PROJECT_SOURCE_DIR}/include/util/mgpualloc.h
${PROJECT_SOURCE_DIR}/include/util/util.h
${PROJECT_SOURCE_DIR}/include/util/format.h
${PROJECT_SOURCE_DIR}/include/util/static.h
${PROJECT_SOURCE_DIR}/include/util/mgpucontext.h
)
SET(COMMON_SRC
${PROJECT_SOURCE_DIR}/src/mmio.cpp
${PROJECT_SOURCE_DIR}/src/sparsematrix.cpp
${PROJECT_SOURCE_DIR}/src/mgpucontext.cu
${PROJECT_SOURCE_DIR}/src/mgpuutil.cpp
)
PRINTLIST("Common Includes are:" "${COMMON_INC}")
PRINTLIST("Common Sources are:" "${COMMON_SRC}")

SET(COMMON_INC_DIR 
${PROJECT_SOURCE_DIR}/include/kernels
${PROJECT_SOURCE_DIR}/include/device
${PROJECT_SOURCE_DIR}/include/util
${PROJECT_SOURCE_DIR}/include/
)

# include projects
add_subdirectory(demo)

MESSAGE(STATUS "Added ${MYPROJECT_CXX_FLAGS} to CMAKE_CXX and CMAKE_C_FLAGS: ${CMAKE_CXX_FLAGS} and ${CMAKE_C_FLAGS}")
