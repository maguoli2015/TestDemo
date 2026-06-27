cmake_minimum_required(VERSION 3.21)

project(TestDemo
    VERSION 1.0.0
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

set(PROJECT_ROOT_DIR "${CMAKE_CURRENT_SOURCE_DIR}")
set(PROJECT_SRC_DIR "${PROJECT_ROOT_DIR}/src")
set(PROJECT_THIRD_DIR "${PROJECT_ROOT_DIR}/third")

if(NOT DEFINED PROJECT_OUTPUT_CONFIG)
    if(CMAKE_BUILD_TYPE)
        set(PROJECT_OUTPUT_CONFIG "${CMAKE_BUILD_TYPE}")
    else()
        set(PROJECT_OUTPUT_CONFIG "$<CONFIG>")
    endif()
endif()

if(NOT DEFINED PROJECT_OUTPUT_ARCH)
    if(CMAKE_GENERATOR_PLATFORM MATCHES "^[Ww]in32$")
        set(PROJECT_OUTPUT_ARCH "x86")
    elseif(CMAKE_GENERATOR_PLATFORM)
        set(PROJECT_OUTPUT_ARCH "${CMAKE_GENERATOR_PLATFORM}")
    elseif(CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(PROJECT_OUTPUT_ARCH "x64")
    else()
        set(PROJECT_OUTPUT_ARCH "x86")
    endif()
endif()

set(PROJECT_OUTPUT_DIR "${PROJECT_ROOT_DIR}/build/${PROJECT_OUTPUT_CONFIG}/${PROJECT_OUTPUT_ARCH}")

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_OUTPUT_DIR}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_OUTPUT_DIR}")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_OUTPUT_DIR}/lib")
set(CMAKE_PDB_OUTPUT_DIRECTORY "${PROJECT_OUTPUT_DIR}/pdb")

foreach(config DEBUG RELEASE RELWITHDEBINFO MINSIZEREL)
    string(TOLOWER "${config}" config_lower)
    if(config STREQUAL "DEBUG")
        set(config_name "Debug")
    elseif(config STREQUAL "RELEASE")
        set(config_name "Release")
    elseif(config STREQUAL "RELWITHDEBINFO")
        set(config_name "RelWithDebInfo")
    else()
        set(config_name "MinSizeRel")
    endif()

    set(config_output_dir "${PROJECT_ROOT_DIR}/build/${config_name}/${PROJECT_OUTPUT_ARCH}")
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${config} "${config_output_dir}")
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY_${config} "${config_output_dir}")
    set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY_${config} "${config_output_dir}/lib")
    set(CMAKE_PDB_OUTPUT_DIRECTORY_${config} "${config_output_dir}/pdb")
endforeach()

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets)

file(GLOB_RECURSE PROJECT_SOURCES CONFIGURE_DEPENDS
    "${PROJECT_SRC_DIR}/*.cpp"
    "${PROJECT_SRC_DIR}/*.h"
    "${PROJECT_SRC_DIR}/*.hpp"
    "${PROJECT_SRC_DIR}/*.ui"
    "${PROJECT_SRC_DIR}/*.qrc"
)

if(QT_VERSION_MAJOR GREATER_EQUAL 6)
    qt_add_executable(${PROJECT_NAME}
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
else()
    add_executable(${PROJECT_NAME}
        ${PROJECT_SOURCES}
    )
endif()

target_include_directories(${PROJECT_NAME}
    PRIVATE
        "${PROJECT_SRC_DIR}"
        "${PROJECT_THIRD_DIR}"
)

target_link_libraries(${PROJECT_NAME}
    PRIVATE
        Qt${QT_VERSION_MAJOR}::Widgets
)

if(EXISTS "${PROJECT_THIRD_DIR}/CMakeLists.txt")
    add_subdirectory("${PROJECT_THIRD_DIR}")
endif()

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(${PROJECT_NAME})
endif()
