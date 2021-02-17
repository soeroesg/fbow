# ----------------------------------------------------------------------------
#   Basic configuration
# ----------------------------------------------------------------------------
cmake_minimum_required(VERSION 3.10)

project(fbow CXX)

set(PROJECT_VERSION "0.0.1")
string(REGEX MATCHALL "[0-9]" PROJECT_VERSION_PARTS "${PROJECT_VERSION}")
list(GET PROJECT_VERSION_PARTS 0 PROJECT_VERSION_MAJOR)
list(GET PROJECT_VERSION_PARTS 1 PROJECT_VERSION_MINOR)
list(GET PROJECT_VERSION_PARTS 2 PROJECT_VERSION_PATCH)
set(PROJECT_SOVERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")

set(CMAKE_MACOSX_RPATH 1)

# ----------------------------------------------------------------------------
#   Build type
# ----------------------------------------------------------------------------

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

# ----------------------------------------------------------------------------
#   Library names and directries
# ----------------------------------------------------------------------------

#TODO
set(PROJECT_DLL_VERSION "${PROJECT_VERSION_MAJOR}${PROJECT_VERSION_MINOR}${PROJECT_VERSION_PATCH}")

if(WIN32)
    # Postfix of DLLs:
    #TODO:
    set(PROJECT_DLLVERSION "${PROJECT_VERSION_MAJOR}${PROJECT_VERSION_MINOR}${PROJECT_VERSION_PATCH}")
    set(RUNTIME_OUTPUT_PATH "${PROJECT_BINARY_DIR}/bin" CACHE PATH "Directory for DLLs and binaries")
    set(EXECUTABLE_OUTPUT_PATH "${PROJECT_BINARY_DIR}/bin" CACHE PATH "Directory for binaries")
    set(LIBRARY_OUTPUT_PATH "${PROJECT_BINARY_DIR}/bin" CACHE PATH "Directory for DLLs")
else()
    # Postfix of so's:
    set(PROJECT_DLLVERSION) # TODO
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_INSTALL_PREFIX}/lib/cmake/" "/usr/lib/cmake" "/usr/local/lib/cmake")

endif()

option(USE_OWN_EIGEN3 "Set to OFF to use a standard eigen3 version" ON) # TODO: there is no eigen in the repo
option(BUILD_UTILS "Set to ON to build utils" ON) # TODO
option(BUILD_TESTS "Set to ON to build tests" ON) # TODO
option(BUILD_SHARED_LIBS "Set to ON to build shared libraries" ON) # TODO
option(USE_CONTRIB "Set ON to use OpenCV contrib modules" OFF) # TODO: rename to USE_OPENCV_NONFREE

# TODO: instruction sets are defined again below
option(USE_AVX "Set AVX instructions on/off" ON)
option(USE_SSE3 "Set SSE3 instructions on/off" ON)
option(USE_SSE4 "Set SSE4 instructions on/off" ON)
option(FBOW_DEVINSTALL "Set to OFF to disable source installation" ON) # TODO: INSTALL_FBOW_DEV

# ----------------------------------------------------------------------------
#   Find dependencies
# ----------------------------------------------------------------------------
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS}) # TODO: no global include!

if(USE_CONTRIB) # TODO: rename to USE_OPENCV_NONFREE
    add_definitions(-DUSE_CONTRIB)
endif()
if(NOT OpenCV_VERSION VERSION_LESS "3.0") # TODO: remove?
    ADD_DEFINITIONS(-DOPENCV_VERSION_3)
    SET(OPENCV_VERSION_3 ON)
else()
    SET(OPENCV_VERSION_3 OFF)
endif()

set(REQUIRED_LIBRARIES ${REQUIRED_LIBRARIES} ${OpenCV_LIBS})

# ----------------------------------------------------------------------------
#   PROJECT CONFIGURATION
#   force some variables that could be defined in the command line to be written to cache
# ----------------------------------------------------------------------------
# TODO: remove, there is no Doxygen documentation in the repo
OPTION(INSTALL_DOC "Set to ON to build/install Documentation" OFF)
IF (INSTALL_DOC)
    FIND_PACKAGE(Doxygen REQUIRED)
    MESSAGE( STATUS "INSTALL_DOC:         ${INSTALL_DOC} ")
    INCLUDE("${PROJECT_SOURCE_DIR}/generateDoc.cmake")
    GENERATE_DOCUMENTATION(${PROJECT_SOURCE_DIR}/dox.in)
ENDIF()

# ----------------------------------------------------------------------------
#   Uninstall target, for "make uninstall"
# ----------------------------------------------------------------------------
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in" "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake" IMMEDIATE @ONLY)
add_custom_target(uninstall "${CMAKE_COMMAND}" -P "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake")

# ----------------------------------------------------------------------------
#   Create configuration file from .in file
#   (If you use windows take care with paths)
# ----------------------------------------------------------------------------

configure_file("${PROJECT_SOURCE_DIR}/config.cmake.in" "${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
install(FILES "${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake" DESTINATION "share/${PROJECT_NAME}/")


# ----------------------------------------------------------------------------
#   Program optimization and debug options
# ----------------------------------------------------------------------------
set(WARNINGS_ARE_ERRORS OFF CACHE BOOL "Treat warnings as errors")
set(WHOLE_PROGRAM_OPTIMIZATION OFF CACHE BOOL "Flags for whole program optimization.")

set(EXTRA_C_FLAGS "")
set(EXTRA_C_FLAGS_RELEASE "")
set(EXTRA_C_FLAGS_DEBUG "")

set(EXTRA_EXE_LINKER_FLAGS "")
set(EXTRA_EXE_LINKER_FLAGS_RELEASE "")
set(EXTRA_EXE_LINKER_FLAGS_DEBUG "")

if(CMAKE_CXX_COMPILER_ID MATCHES "GNU" OR CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR MINGW)
    # profiling option
    set(ENABLE_PROFILING OFF CACHE BOOL "Enable profiling in the GCC/Clang compiler (Add flags: -g -pg)")
    # option for omitting frame pointer
    set(USE_OMIT_FRAME_POINTER ON BOOL "Enable -fomit-frame-pointer for GCC/Clang")

    # select optimization level
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES arm*)
        # ARM system
        set(USE_O2 ON CACHE BOOL "Enable -O2 for GCC/Clang")
        set(USE_FAST_MATH OFF CACHE BOOL "Enable -ffast-math for GCC/Clang")
    endif()
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES powerpc*)
        # PowerPC system
        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC/Clang")
        set(USE_POWERPC ON CACHE BOOL "Enable PowerPC for GCC/Clang")
    endif ()
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES amd64* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES x86_64*)
        # 64bit system
        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC/Clang")
        set(USE_FAST_MATH OFF CACHE BOOL "Enable -ffast-math for GCC/Clang")
        set(USE_MMX ON CACHE BOOL "Enable MMX for GCC/Clang")
        set(USE_SSE ON CACHE BOOL "Enable SSE for GCC/Clang")
        set(USE_SSE2 ON CACHE BOOL "Enable SSE2 for GCC/Clang")
        set(USE_SSE3 ON CACHE BOOL "Enable SSE3 for GCC/Clang")
        set(USE_SSE4 ON CACHE BOOL "Enable SSE4 for GCC/Clang")
    endif()
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES i686* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES x86)
        # 32bit system
        set(USE_O3 ON CACHE BOOL "Enable -O3 for GCC/Clang")
        set(USE_FAST_MATH OFF CACHE BOOL "Enable -ffast-math for GCC/Clang")
        set(USE_MMX ON CACHE BOOL "Enable MMX for GCC/Clang")
        set(USE_SSE OFF CACHE BOOL "Enable SSE for GCC/Clang")
        set(USE_SSE2 OFF CACHE BOOL "Enable SSE2 for GCC/Clang")
        set(USE_SSE3 OFF CACHE BOOL "Enable SSE3 for GCC/Clang")
        set(USE_SSE4 OFF CACHE BOOL "Enable SSE4 for GCC/Clang")
    endif()
    set(USE_AVX ON CACHE BOOL "Enable AVX for GCC/Clang")

    # warning options
    set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Wall")
    if(WARNINGS_ARE_ERRORS)
        set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Werror")
    endif()

    # The -Wno-long-long is required in 64bit systems when including sytem headers.
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES x86_64* OR ${CMAKE_SYSTEM_PROCESSOR} MATCHES amd64*)
        set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -Wno-long-long")
    endif()

    # apply options
    #if(WHOLE_PROGRAM_OPTIMIZATION)
    #    set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -fwhole-program --combine")
    #endif()
    # TODO: NOT ENABLE_PROFILING AND
    if(USE_OMIT_FRAME_POINTER)
       set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -fomit-frame-pointer")
    endif()
    if(USE_O2)
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -O2")
    endif()
    if(USE_O3)
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -O3")
    endif()
    if(USE_FAST_MATH)
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -ffast-math")
    endif()
    if(USE_POWERPC)
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -mcpu=G3 -mtune=G5")
    endif()

    # apply vectorization
    if(USE_MMX)
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -mmmx")
        add_definitions(-DUSE_MMX)
    endif()
    if(USE_SSE)
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -msse")
        add_definitions(-DUSE_SSE)
    endif()
    if(USE_SSE2)
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -msse2")
        add_definitions(-DUSE_SSE2)
    endif()
    if(USE_SSE3 AND NOT MINGW) # SSE3 should be disabled under MingW because it generates compiler errors
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -msse3")
        add_definitions(-DUSE_SSE3)
    endif()
    if(USE_SSE4 AND NOT MINGW)
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -msse4.1")
        add_definitions(-DUSE_SSE4)
    endif()
    IF(USE_AVX)
        set(VECTORIAL_INSTRUCTIONS "${VECTORIAL_INSTRUCTIONS} -mavx")
        add_definitions(-DUSE_AVX)
    endif()

    if(ENABLE_PROFILING)
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE} -pg -g")
    else()
        if(NOT APPLE)
            set(EXTRA_C_FLAGS "${EXTRA_C_FLAGS} -ffunction-sections")
        endif()
    endif()

    # TODO: what is this for?
    if(${CMAKE_SYSTEM_PROCESSOR} MATCHES armv7l) # In ARM_Cortex8 with neon, enable vectorized operations
        set(EXTRA_C_FLAGS_RELEASE "${EXTRA_C_FLAGS_RELEASE}")
    endif()

    # TODO: unify compiler flags
    set(CMAKE_CXX_FLAGS_RELEASE "${VECTORIAL_INSTRUCTIONS} ${EXTRA_C_FLAGS_RELEASE} -O3 -std=c++11 -Wall -DNDEBUG")
    set(CMAKE_CXX_FLAGS_DEBUG "${VECTORIAL_INSTRUCTIONS} -g3 -DDEBUG -D_DEBUG -std=c++11 -Wall")
    set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "${VECTORIAL_INSTRUCTIONS} -O2 -g3 -DDEBUG -D_DEBUG -std=c++11 -Wall")

    message(STATUS "vec=${VECTORIAL_INSTRUCTIONS}")
    message(STATUS "-------------------------------------------------------------------------------" )
    message(STATUS "GNU COMPILER")
    message(STATUS "-------------------------------------------------------------------------------" )

else() # TODO: MSVC
    add_definitions(-DNOMINMAX)
endif() #END OF COMPILER SPECIFIC OPTIONS


find_package(OpenMP)
if(OPENMP_FOUND)
    add_compile_options(-DUSE_OPENMP)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()


set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} ${CMAKE_CXX_FLAGS} ")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${CMAKE_CXX_FLAGS} ")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "${CMAKE_CXX_FLAGS_RELWITHDEBINFO} ${CMAKE_CXX_FLAGS} ")
SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${EXTRA_EXE_LINKER_FLAGS}")
SET(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS_RELEASE} ${EXTRA_EXE_LINKER_FLAGS_RELEASE}")
SET(CMAKE_EXE_LINKER_FLAGS_DEBUG "${CMAKE_EXE_LINKER_FLAGS_DEBUG} ${EXTRA_EXE_LINKER_FLAGS_DEBUG}")


#------------------------------------------------
# DIRS
#------------------------------------------------
add_subdirectory(src)

if(BUILD_UTILS)
    add_subdirectory(utils)
endif()

if(BUILD_TESTS)
    add_subdirectory(tests)
endif()


# ----------------------------------------------------------------------------
# Display status message for important variables
# ----------------------------------------------------------------------------

message(STATUS)
message(STATUS "-------------------------------------------------------------------------------" )
message(STATUS "General configuration for ${PROJECT_NAME} ${PROJECT_VERSION}")
message(STATUS "-------------------------------------------------------------------------------" )
message(STATUS)
message(STATUS "CMAKE_MODULE_PATH = ${CMAKE_MODULE_PATH}")
message(STATUS "CMAKE_INSTALL_PREFIX = ${CMAKE_INSTALL_PREFIX}")
message(STATUS "CMAKE_BINARY_DIR = ${CMAKE_BINARY_DIR}")
message(STATUS)
message(STATUS "C compiler: ${CMAKE_C_COMPILER}")
message(STATUS "C++ compiler: ${CMAKE_CXX_COMPILER}")
message(STATUS "Processor: ${CMAKE_SYSTEM_PROCESSOR}")
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "Build shared libs: ${BUILD_SHARED_LIBS}")
message(STATUS "C++ flags (Common):${CMAKE_CXX_FLAGS}")
message(STATUS "C++ flags (Release): ${CMAKE_CXX_FLAGS_RELEASE}")
message(STATUS "C++ flags (Debug): ${CMAKE_CXX_FLAGS_DEBUG}")
message(STATUS "C++ flags (RelWithDebInfo): ${CMAKE_CXX_FLAGS_RELWITHDEBINFO}")
message(STATUS)
message(STATUS "BUILD_UTILS= ${BUILD_UTILS}")
message(STATUS "BUILD_TESTS= ${BUILD_TESTS}")
message(STATUS "OPENMP FOUND: ${OPENMP_FOUND}")
message(STATUS "OpenCV_DIR = ${OpenCV_DIR}" )
message(STATUS "OpenCV_INCLUDE_DIRS = ${OpenCV_INCLUDE_DIRS}")
message(STATUS "OpenCV_LIBS = ${OpenCV_LIBS}")
message(STATUS)
message(STATUS "Change a value with: cmake -D<Variable>=<Value>" )
message(STATUS)
