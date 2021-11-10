cmake_minimum_required(VERSION 3.15)

project(QtSerialDPF CXX)

find_package(fmt REQUIRED)
find_package(range-v3 REQUIRED)
find_package(cxxopts REQUIRED)
find_package(Qt5Core REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Gui REQUIRED)

add_executable(QtSerialDPF src/main.cxx)
target_compile_features(QtSerialDPF PRIVATE cxx_std_17)

target_link_libraries(QtSerialDPF
  PRIVATE
    fmt::fmt
    range-v3::range-v3
    cxxopts::cxxopts
    Qt5::Core
    Qt5::Widgets
    Qt5::Gui
    )

set_target_properties( QtSerialDPF
  PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/archive"
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/library"
  RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/runtime"
)

# store git log info
add_custom_command(TARGET QtSerialDPF
  PRE_BUILD
  WORKING_DIRECTORY "${CMAKE_SOURCE_DIR}"
  COMMAND git log -1 --format=medium --decorate=short > VERSION
)

add_custom_command(TARGET QtSerialDPF
  POST_BUILD
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/runtime
  COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_SOURCE_DIR}/README.md" .
  COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_SOURCE_DIR}/LICENSE" .
  COMMAND ${CMAKE_COMMAND} -E copy "${CMAKE_SOURCE_DIR}/VERSION" .
  COMMAND ${CMAKE_COMMAND} -E tar -cfv "${CMAKE_BINARY_DIR}/${CMAKE_PROJECT_NAME}-${CMAKE_SYSTEM_NAME}.zip" --format=zip .
)
