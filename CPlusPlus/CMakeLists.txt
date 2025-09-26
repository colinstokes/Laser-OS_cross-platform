cmake_minimum_required(VERSION 3.10)
project(LaserOS_CHOP VERSION 1.0.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set the plugin name
set(PLUGIN_NAME "Laser OS CHOP")

# Source files
set(SOURCES
    CHOP_Buffer.cpp
    dllmain.cpp
    Laser_OS_CHOP.cpp
    pch.cpp
)

# Header files
set(HEADERS
    CHOP_Buffer.h
    CHOP_CPlusPlusBase.h
    CPlusPlus_Common.h
    GL_Extensions.h
    Laser_OS_CHOP.h
    pch.h
)

# Include directories
include_directories(
    lib/include
    .
)

# Platform-specific settings
if(WIN32)
    # Windows settings
    add_definitions(-D_WIN32 -D_WINDOWS -D_USRDLL)
    set(LIBRARY_NAME "Laser OS CHOP")
    set(LASERDOCK_LIB "lib/laserdocklib.lib")

elseif(APPLE)
    # macOS settings
    add_definitions(-D_MACOSX)
    set(LIBRARY_NAME "Laser OS CHOP")
    set(LASERDOCK_LIB "/Users/colinstokes/Documents/C++ Projects/LaserOS/Laser_OS_TouchDesigner/CPlusPlus/lib/liblaserdocklib.dylib")

    # Set bundle properties for macOS
    set(MACOSX_BUNDLE_GUI_IDENTIFIER "net.prim8.${PROJECT_NAME}")
    set(MACOSX_BUNDLE_BUNDLE_NAME ${PLUGIN_NAME})
    set(MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION})
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION})

else()
    # Linux settings (if needed)
    add_definitions(-D_LINUX)
    set(LIBRARY_NAME "Laser OS CHOP")
    set(LASERDOCK_LIB "lib/liblaserdocklib.so")
endif()

# Create the shared library
add_library(${PROJECT_NAME} SHARED ${SOURCES} ${HEADERS})

# Link libraries
target_link_libraries(${PROJECT_NAME} ${LASERDOCK_LIB})

# Set output properties
if(APPLE)
    set_target_properties(${PROJECT_NAME} PROPERTIES
        OUTPUT_NAME "${LIBRARY_NAME}"
        SUFFIX ".dll"
        PREFIX ""
        BUNDLE FALSE
    )
elseif(WIN32)
    set_target_properties(${PROJECT_NAME} PROPERTIES
        OUTPUT_NAME "${LIBRARY_NAME}"
    )
endif()

# Precompiled header
if(MSVC)
    set_source_files_properties(pch.cpp PROPERTIES COMPILE_FLAGS "/Ycpch.h")
    set_source_files_properties(${SOURCES} PROPERTIES COMPILE_FLAGS "/Yupch.h")
endif()