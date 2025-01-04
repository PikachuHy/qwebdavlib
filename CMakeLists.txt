cmake_minimum_required(VERSION 3.19)
project(QtWebDAV LANGUAGES CXX)

include(GenerateExportHeader)

# Set default options
option(BUILD_EXAMPLE "Build with example" OFF)
option(BUILD_WITH_QT6 "Explicitly build with Qt6" OFF)

# Determine Qt version to use
if (BUILD_WITH_QT6)
    set(QT_VERSION_MAJOR 6)
else()
    set(QT_VERSION_MAJOR 5)
endif()

# Find required Qt components
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Network Xml REQUIRED)

set(QtWebDAV_SOURCES
    qnaturalsort.cpp
    qwebdav.cpp
    qwebdavdirparser.cpp
    qwebdavitem.cpp
)

set(QtWebDAV_HEADERS
    qnaturalsort.h
    qwebdav.h
    qwebdav_global.h
    qwebdavdirparser.h
    qwebdavitem.h
)

# Define library target
add_library(QtWebDAV
    ${QtWebDAV_SOURCES}
    ${QtWebDAV_HEADERS}
)

# Set target properties
set_target_properties(QtWebDAV PROPERTIES
    AUTOMOC ON
    VERSION 1.0
    SOVERSION 1
    EXPORT_NAME QtWebDAV
)

generate_export_header(
  QtWebDAV
  BASE_NAME QTWEBDAV
  EXPORT_FILE_NAME qwebdav_global.h
)

list(APPEND QtWebDAV_HEADERS ${CMAKE_CURRENT_BINARY_DIR}/qwebdav_global.h)

# Specify include directories and compile definitions
target_include_directories(QtWebDAV PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<INSTALL_INTERFACE:include>
)
target_compile_definitions(QtWebDAV PRIVATE DEBUG_WEBDAV)

# Link Qt libraries
target_link_libraries(QtWebDAV PUBLIC
    Qt${QT_VERSION_MAJOR}::Network
    Qt${QT_VERSION_MAJOR}::Xml
)

# Installation
include(GNUInstallDirs)
install(TARGETS QtWebDAV
    EXPORT QtWebDAVTargets
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
install(FILES ${QtWebDAV_HEADERS}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/QtWebDAV
)

# Export package
install(EXPORT QtWebDAVTargets
    FILE QtWebDAVTargets.cmake
    NAMESPACE QtWebDAV::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/QtWebDAV
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/QtWebDAVConfigVersion.cmake
    VERSION 1.0
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/QtWebDAVConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/QtWebDAVConfig.cmake
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/QtWebDAV
)

install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/QtWebDAVConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/QtWebDAVConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/QtWebDAV
)

# Optionally build example
if (BUILD_EXAMPLE)
    add_subdirectory(example)
endif()
