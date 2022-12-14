cmake_minimum_required(VERSION 3.25)

# Note that you'll want to change the Visual Studio "Build and Run" options so only the startup project gets built,
# otherwise the office project will try to build and fail horribly. It's setup for intellisense, not building.
#
# The startup project is a CMake custom target named "powerpoint" that copies the Babylon.js JavaScript files to the
# appropriate Office folders when built, and launches PowerPoint when run.

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

project(office-dev)

set(SOURCE_GLOBS
    C:/office/src/gfx/*
    C:/office/src/graphics/*
)
set(INCLUDE_DIR_GLOBS
    C:/NugetCache/babylon.native.*/build/native/include
    C:/NugetCache/microsoft.gltf.cpp.*/build/native/include
    C:/NugetCache/microsoft.lift.core.cx.*/build/native/include  
    C:/NugetCache/microsoft.lift.engine.cpp.*/build/native/include  
    C:/NugetCache/office.nodejs.*/include/node
    C:/office/ICache/current/graphics_spectre/x64/debug/x-none/publicinc
    C:/office/Import/x64/debug/gfx/x-none/x64/inc # TODO: This has a lot of the same headers as in graphics_spectre. Break gfx and graphics into separate projects with separate include paths if it becomes a problem.
    C:/office/Import/x64/debug/graphics_spectre/x-none/x64/inc
    C:/office/src/otools/inc
    C:/office/src/otools/inc/arc
    C:/office/src/otools/inc/gfx
)
set(DEFINES
    NODE_ADDON_API_DISABLE_DEPRECATED

    # Avoid intellisense error "expected an identifier" on std::numeric_limits<size_t>::max() in C:\NugetCache\microsoft.lift.core.cx.2.8.3.55\build\native\include\CoreUtils\Gltf.h
    # See https://stackoverflow.com/a/7035078
    NOMINMAX
)

# Run the source globs twice, once with directories and once without, then remove the files from the one with directories to get just the directories.
file(GLOB_RECURSE SOURCES LIST_DIRECTORIES false ${SOURCE_GLOBS})
file(GLOB_RECURSE SOURCE_DIRS LIST_DIRECTORIES true ${SOURCE_GLOBS})
list(REMOVE_ITEM SOURCE_DIRS ${SOURCES})

file(GLOB INCLUDE_DIRS ${INCLUDE_DIR_GLOBS})

set(CMAKE_CXX_STANDARD 23)

add_library(office ${SOURCES})
target_compile_definitions(office PUBLIC ${DEFINES})
target_include_directories(office PUBLIC ${SOURCE_DIRS} ${INCLUDE_DIRS})

source_group(TREE C:/office FILES ${SOURCES})

add_custom_target(powerpoint ALL COMMAND scripts\\copy-babylon-scripts.bat WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT powerpoint)
set_target_properties(powerpoint PROPERTIES
    VS_DEBUGGER_COMMAND "C:\\office\\Install\\x64\\debug\\devclient\\FILES\\PFILES\\MSOFFICE\\Office16\\POWERPNT.EXE"
    VS_DEBUGGER_WORKING_DIRECTORY "C:\\office\\Install\\x64\\debug\\devclient\\FILES\\PFILES\\MSOFFICE\\Office16"
)
