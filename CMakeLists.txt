cmake_minimum_required(VERSION 3.15)

# User inputs - mandatory:
#   COREMARK_PORT - if not specified and native port is detected, uses native compile.
#   COREMARK_TOTAL_DATA_SIZE - if not specified uses 

option(COREMARK_USE_PGO "Builds with Profile Guidance Options"  OFF) # TODO(phelter): Just added not implemented.
set(COREMARK_ITERATIONS  "0" CACHE STRING "Number of iterations to run")
set(COREMARK_TOTAL_DATA_SIZE "2000" CACHE STRING "Total Data Size to for memory block") # See core_portme.h for details.
set(COREMARK_FLAGS_STRING    "Unknown" CACHE STRING "Flags used to compile")

# -------------------------------------------------------------------
# Detect CoreMark Port option.
if(NOT COREMARK_PORT)
    message(WARNING " COREMARK_PORT is not set. Please specify it from top-level CMake file (example):\n"
                    "  set(COREMARK_PORT BAREBONES CACHE STRING \"\")\n"
                    " or from CMake command line option:\n"
                    "  -DCOREMARK_PORT=BAREBONES\n"
                    " \n"
                    " Available port options:\n"
                    " A_CUSTOM_PORT - Compiler: User Defined, Target: User Defined\n"
                    " BAREBONES     - Compiler: User Defined, Target: Bare bones CPU (none-eabi etc.)\n"
                    " POSIX         - Compiler: User Defined, Target: Posix OS - CygWin, FreeBSD, Linux, MacOS\n"
                    " RTEMS         - Compiler: User Defined, Target: RTEMS OS\n"
                    " SIMPLE        - Compiler: User Defined, Target: Simple (has stdio)\n"
                    " ZEPHYR        - Compiler: User Defined, Target: Zephyr RTOS\n")

    if (CYGWIN OR BSD OR UNIX)
        message(STATUS " Auto-Detected Posix, setting COREMARK_PORT=POSIX")
        set(COREMARK_PORT POSIX CACHE STRING "CoreMark port name")
    endif()
    # Cannot detect BAREBONES, RTEMS, SIMPLE ZEPHYR
elseif((COREMARK_PORT STREQUAL "A_CUSTOM_PORT") AND (NOT TARGET CoreMark::port))
    message(STATUS "COREMARK_PORT is set to A_CUSTOM_PORT. Ensure there is an 'add_library(CoreMark::port ALIAS <your library>)' in your project. See Barebones for an example.")
endif()

# -------------------------------------------------------------------
# Ports
add_subdirectory(barebones)
add_subdirectory(posix)
add_subdirectory(rtems)
add_subdirectory(simple)

# -------------------------------------------------------------------
add_library(coremark_api INTERFACE)
add_library(CoreMark::api ALIAS coremark_api)

target_sources(coremark_api
    INTERFACE
        coremark.h
)

target_include_directories(coremark_api
    INTERFACE
        .
)

# -------------------------------------------------------------------
add_executable(coremark)

target_sources(coremark
    PRIVATE
        core_list_join.c
        core_main.c
        core_matrix.c
        core_state.c
        core_util.c
)

target_include_directories(coremark
    PUBLIC
    .
)

target_compile_definitions(coremark
    PRIVATE
        ITERATIONS=${COREMARK_ITERATIONS}
        TOTAL_DATA_SIZE=${COREMARK_TOTAL_DATA_SIZE}
        FLAGS_STR="${COREMARK_FLAGS_STRING}"
        # Don't know why this doesn't work but should be able to set 
        # FLAGS_STR ="$<TARGET_GENEX_EVAL:coremark,$<TARGET_PROPERTY:coremark,COMPILE_OPTIONS>>"
        # But this results in Unterminated quoted string.
)

target_link_libraries(coremark
    PRIVATE
        CoreMark::api
        CoreMark::port
)
