cmake_minimum_required(VERSION 3.24)
project(imgui)

# Set the output directory for the built library and include files
set(TARGET_LIB_DIR "${CMAKE_BINARY_DIR}/lib")
set(TARGET_INCLUDE_DIR "${CMAKE_BINARY_DIR}/include")

file(GLOB IMGUI_INCLUDE
        ${CMAKE_SOURCE_DIR}/*.h
        backends/imgui_impl_dx12.h
        backends/imgui_impl_win32.h
)

file(GLOB IMGUI_SOURCES
        ${CMAKE_SOURCE_DIR}/*.cpp
        backends/imgui_impl_dx12.cpp
        backends/imgui_impl_win32.cpp
)

add_library(imgui STATIC ${IMGUI_SOURCES})

# Specify include directories
target_include_directories(imgui PUBLIC ${CMAKE_SOURCE_DIR})

# Set output directory for the library
set_target_properties(imgui PROPERTIES
        ARCHIVE_OUTPUT_DIRECTORY ${TARGET_LIB_DIR}
        ARCHIVE_OUTPUT_DIRECTORY_RELEASE ${TARGET_LIB_DIR}
)

# Enable multi-threaded builds for Visual Studio
if (MSVC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /MP")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP")
endif ()

# Post-build step to move include files to the target include directory
add_custom_command(
        TARGET imgui POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E make_directory ${TARGET_INCLUDE_DIR}
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${IMGUI_INCLUDE} ${TARGET_INCLUDE_DIR}
)
