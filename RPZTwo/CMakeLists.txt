# Generated Cmake Pico project file

cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Initialise pico_sdk from installed location
# (note this can come from environment, CMake cache etc)

# == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==
if(WIN32)
    set(USERHOME "$ENV{USERPROFILE}")
else()
    set(USERHOME "$ENV{HOME}")
endif()
set(sdkVersion 2.1.1)
set(toolchainVersion 14_2_Rel1)
set(picotoolVersion 2.1.1)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
# ====================================================================================
set(PICO_BOARD pico2_w CACHE STRING "Board type")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

project(RPZTwo C CXX ASM)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()

# Search PIO files if any
file(GLOB PIO_SOURCES "${CMAKE_CURRENT_LIST_DIR}/PIO/*.pio")

# Create generated folder if not exists
file(MAKE_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/generated)

# Find all source and header files
file(GLOB_RECURSE SRC_FILES CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_LIST_DIR}/src/*.c"
)

file(GLOB_RECURSE INCLUDE_DIRS
    RELATIVE ${CMAKE_CURRENT_LIST_DIR}
    "${CMAKE_CURRENT_LIST_DIR}/src/*"
)

# Add executable. Default name is the project name, version 0.1
add_executable(
    RPZTwo 
    RPZTwoMain.c
    ${PIO_SOURCES}
    ${SRC_FILES})

pico_set_program_name(RPZTwo "MulticoreTest")
pico_set_program_version(RPZTwo "0.1")

# Modify the below lines to enable/disable output over UART/USB
pico_enable_stdio_uart(RPZTwo 0)
pico_enable_stdio_usb(RPZTwo 0)

# Add the standard library to the build
target_link_libraries(
    RPZTwo
    pico_stdlib
    pico_multicore)

# Add the standard include files to the build
target_include_directories(
    RPZTwo
    PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}/generated
    ${INCLUDE_DIRS}
)

foreach(pio_file ${PIO_SOURCES})
    get_filename_component(pio_filename ${pio_file} NAME_WE)
    
    # Set header file paths
    set(output_dir ${CMAKE_CURRENT_LIST_DIR}/generated)

    # Place PIO headers in generated folder
    pico_generate_pio_header(RPZTwo  ${pio_file}
        OUTPUT_DIR ${output_dir}
    )
endforeach()

# Add any user requested libraries
target_link_libraries(
    RPZTwo 
    pico_cyw43_arch_none
    hardware_pio
    hardware_clocks)

pico_add_extra_outputs(RPZTwo)

