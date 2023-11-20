cmake_minimum_required(VERSION 3.18)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(Qt5 REQUIRED COMPONENTS
Core Widgets OpenGL PrintSupport)
find_package(VTK 9 REQUIRED COMPONENTS
  CommonCore
  IOXML
)
include_directories(
  ${CMAKE_CURRENT_BINARY_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}
)

if (NOT "${CMAKE_BUILD_TYPE}" STREQUAL "Debug")
  add_definitions(-DQT_NO_DEBUG_OUTPUT)
endif()

set(UIs
  ./src/mainwindow.ui
)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

set(Srcs
  ./src/main.cpp
  ./src/mainwindow.cpp
  ./src/mesh.cpp
  ./src/raycastcanvas.cpp
  ./src/raycastvolume.cpp
  ./src/trackball.cpp
)
set(Qrcs
  ./resources.qrc)

add_executable(${PROJECT_NAME}
  ${Srcs}
  ${Hdrs}
  ${UIs}
  ${Qrcs}
  ${APP_ICON_RESOURCE_WINDOWS}
  .clang-format
)

target_link_libraries(${PROJECT_NAME} PRIVATE Qt5::Widgets Qt5::OpenGL)



target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src)
target_link_libraries(${PROJECT_NAME} PRIVATE ${VTK_LIBRARIES})

if(CMAKE_BUILD_TYPE STREQUAL "Release" AND WIN32)
  set_property(TARGET ${PROJECT_NAME} PROPERTY WIN32_EXECUTABLE true)
endif()

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/CopyFile.cmake
  "file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/shaders DESTINATION ${CMAKE_CURRENT_BINARY_DIR})")

add_custom_target(
  copy_shader_files
  ${CMAKE_COMMAND}
  -D SOURCE_DIR=${CMAKE_CURRENT_SOURCE_DIR}/shaders
  -D DESTINATION_DIR=${CMAKE_CURRENT_BINARY_DIR} 
  -P CopyFile.cmake
  COMMENT "Copying Files for target: test_SphereShader" VERBATIM 
)

add_dependencies(${PROJECT_NAME} copy_shader_files)
