cmake_minimum_required(VERSION 3.16)

set(PRJ_ROOT_NAME TestNodeEditor)
project(${PRJ_ROOT_NAME} VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Core Widgets)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Core Widgets)
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/ThirdParty/nodeeditor)
set(PROJECT_SOURCES
  main.cpp              # just to using as test,  you can modify
  SimpleGraphModel.hpp  # just to using as test,  you can remove
  SimpleGraphModel.cpp  # just to using as test,  you can remove
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
