cmake_minimum_required(VERSION 3.26)
project(Lampray)

set(CMAKE_CXX_STANDARD 17)

option(USE_XDG_DIRECTORY "Use XDG directory to store data instead of the working directory" ON)

add_executable(${PROJECT_NAME} main.cpp
        game-data/gameControl.h
        VERSION
        Lampray/Base/lampBase.h
        Lampray/Filesystem/lampFS.h
        Lampray/Control/lampControl.h
        Lampray/Control/lampControl.cpp
        Lampray/Parse/lampParse.h
        game-data/BG3/BG3.cpp
        game-data/BG3/BG3.h
        Lampray/Control/lampConfig.h
        Lampray/Control/lampConfig.cpp
        Lampray/Filesystem/lampExtract.cpp
        Lampray/Filesystem/lampIO.cpp
        Lampray/Menu/lampMenu.h
        Lampray/Menu/lampMenu.cpp
        Lampray/Filesystem/lampUpdate.cpp
        Lampray/Control/lampGames.h
        Lampray/Filesystem/lampShare.cpp
        Lampray/Menu/lampCustomise.h
        Lampray/Filesystem/lampTrack.cpp
        game-data/C77/C77.cpp
        game-data/C77/C77.h
        game-data/LHL/LHL.cpp
        game-data/LHL/LHL.h
        Lampray/Lang/lampLang.h
)

if(USE_XDG_DIRECTORY) 
  target_compile_definitions(${PROJECT_NAME} PUBLIC USE_XDG)
endif(USE_XDG_DIRECTORY)

find_package(PkgConfig REQUIRED)

# Include the json package
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/json
  ${CMAKE_BINARY_DIR}/json-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    json
)
target_include_directories(${PROJECT_NAME}
  PRIVATE
    ${CMAKE_SOURCE_DIR}/third-party/json/lib
)

# Include the imgui package via FetchContent
include(FetchContent)
FetchContent_Declare(
  imgui
  GIT_REPOSITORY https://github.com/ocornut/imgui.git
  GIT_TAG        v1.89.6
)
FetchContent_MakeAvailable(imgui)

target_link_libraries(${PROJECT_NAME}
  PRIVATE
    imgui
)

# Include the bit7z package
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/bit7z
  ${CMAKE_BINARY_DIR}/bit7z-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    bit7z
)
target_include_directories(${PROJECT_NAME}
  PRIVATE
    ${CMAKE_SOURCE_DIR}/third-party/bit7z/lib
)

# Include the nfd package
add_library(nfd STATIC
  third-party/nfd/src/nfd_common.c
  third-party/nfd/src/nfd_cocoa.m
)
target_include_directories(nfd
  PUBLIC ${CMAKE_SOURCE_DIR}/third-party/nfd/src/include
)
if(APPLE)
  find_library(APPKIT_FRAMEWORK AppKit)
  target_link_libraries(nfd PRIVATE ${APPKIT_FRAMEWORK})
endif()
target_link_libraries(${PROJECT_NAME} PRIVATE nfd)

# Find the SDL2 package
find_package(SDL2 REQUIRED)

# Include SDL2 headers
include_directories(${SDL2_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${SDL2_LIBRARIES})

# Include the pugixml package
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/pugixml
  ${CMAKE_BINARY_DIR}/pugixml-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    pugixml
)
target_include_directories(${PROJECT_NAME}
  PRIVATE
    ${CMAKE_SOURCE_DIR}/third-party/pugixml/lib
)

find_package(CURL REQUIRED)
include_directories(${CURL_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${CURL_LIBRARIES})

# Include the lz4 package
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/lz4/build/cmake
  ${CMAKE_BINARY_DIR}/lz4-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    lz4
)
target_include_directories(${PROJECT_NAME}
  PRIVATE
    ${CMAKE_SOURCE_DIR}/third-party/lz4/lib
)
