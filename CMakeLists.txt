cmake_minimum_required(VERSION 3.16)

set(PRJ_ROOT_NAME TestNodeEditor)
project(${PRJ_ROOT_NAME} VERSION 0.1 LANGUAGES C CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Widgets)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Widgets)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/ThirdParty/nodeeditor)

file(GLOB_RECURSE SRCS_FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cxx
  ${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
  ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cc
)
file(GLOB_RECURSE HDR_FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h
  ${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp
  ${CMAKE_CURRENT_SOURCE_DIR}/include/*.hxx
)

set(PROJECT_SOURCES
  ${HDR_FILES}
  ${SRCS_FILES}
  ${CMAKE_CURRENT_SOURCE_DIR}/main.cpp
)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
  qt_add_executable(${PRJ_ROOT_NAME}
    MANUAL_FINALIZATION
    ${PROJECT_SOURCES}
  )
else()
  if(ANDROID)
    add_library(${PRJ_ROOT_NAME} SHARED
      ${PROJECT_SOURCES}
    )
else()
  add_executable(${PRJ_ROOT_NAME}
    ${PROJECT_SOURCES}
  )
endif()
endif()

target_include_directories(${PRJ_ROOT_NAME} PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}/include
)
target_link_libraries(${PRJ_ROOT_NAME} PRIVATE
  Qt${QT_VERSION_MAJOR}::Core
  Qt${QT_VERSION_MAJOR}::Widgets
  QtNodes::QtNodes
)

if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.${PRJ_ROOT_NAME})
endif()
set_target_properties(${PRJ_ROOT_NAME} PROPERTIES
  ${BUNDLE_ID_OPTION}
  MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
  MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
  MACOSX_BUNDLE TRUE
  WIN32_EXECUTABLE TRUE
)

if(QT_VERSION_MAJOR EQUAL 6)
  qt_finalize_executable(${PRJ_ROOT_NAME})
endif()
