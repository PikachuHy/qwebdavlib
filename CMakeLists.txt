cmake_minimum_required(VERSION 3.19)
project(QtWebDAV)
find_package(QT NAMES Qt6 Qt5 COMPONENTS Network Xml REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Network Xml REQUIRED)
set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR})
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR})
add_library(QtWebDAV SHARED
        qnaturalsort.cpp
        qnaturalsort.h
        qwebdav.cpp
        qwebdav.h
        qwebdav_global.h
        qwebdavdirparser.cpp
        qwebdavdirparser.h
        qwebdavitem.cpp
        qwebdavitem.h
        )

target_link_libraries(QtWebDAV PUBLIC
        Qt${QT_VERSION_MAJOR}::Network
        Qt${QT_VERSION_MAJOR}::Xml
        )
target_include_directories(QtWebDAV PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_compile_definitions(QtWebDAV PRIVATE -DQWEBDAV_LIBRARY)
target_compile_definitions(QtWebDAV PRIVATE DEBUG_WEBDAV)
set_target_properties(QtWebDAV PROPERTIES AUTOMOC ON)
option(BUILD_EXAMPLE "Build with example")
if (BUILD_EXAMPLE)
    add_subdirectory(example)
endif()
