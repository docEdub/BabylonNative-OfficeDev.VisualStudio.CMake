cmake_minimum_required(VERSION 3.25)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
project(office-dev)
file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS
    C:/office/src/gfx/*
    C:/office/src/graphics/*
)
add_custom_target(office SOURCES ${SOURCES})
source_group(TREE C:/office FILES ${SOURCES})
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT office)
set_target_properties(office PROPERTIES
    VS_DEBUGGER_COMMAND "C:\\office\\Install\\x64\\debug\\devclient\\FILES\\PFILES\\MSOFFICE\\Office16\\POWERPNT.EXE"
    VS_DEBUGGER_WORKING_DIRECTORY "C:\\office\\Install\\x64\\debug\\devclient\\FILES\\PFILES\\MSOFFICE\\Office16"
)
