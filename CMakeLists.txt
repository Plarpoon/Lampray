cmake_minimum_required(VERSION 3.26)
project(Lampray)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
option(USE_XDG_DIRECTORY "Use XDG directory to store data instead of the working directory" ON)

add_executable(${PROJECT_NAME}
    main.cpp
    third-party/imgui/imconfig.h
    third-party/imgui/imgui.cpp
    third-party/imgui/imgui.h
    third-party/imgui/imgui_demo.cpp
    third-party/imgui/imgui_draw.cpp
    third-party/imgui/imgui_internal.h
    third-party/imgui/imgui_tables.cpp
    third-party/imgui/imgui_widgets.cpp
    third-party/imgui/imstb_rectpack.h
    third-party/imgui/imstb_textedit.h
    third-party/imgui/imstb_truetype.h
    third-party/imgui/backends/imgui_impl_sdl2.cpp
    third-party/imgui/backends/imgui_impl_sdlrenderer2.cpp
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

# Enable AddressSanitizer in Debug builds only
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
  message(STATUS "Enabling AddressSanitizer")
  target_compile_options(${PROJECT_NAME} PRIVATE
    -fsanitize=address 
    -fno-omit-frame-pointer
  )
  target_link_options(${PROJECT_NAME} PRIVATE
    -fsanitize=address
  )
endif()

if(USE_XDG_DIRECTORY)
  target_compile_definitions(${PROJECT_NAME} PUBLIC USE_XDG)
endif()

find_package(PkgConfig REQUIRED)

# ----------------------
# ocornut::imgui
# ----------------------
target_include_directories(${PROJECT_NAME}
  PRIVATE
    ${CMAKE_SOURCE_DIR}/third-party/imgui
    ${CMAKE_SOURCE_DIR}/third-party/imgui/backends
)

# ----------------------
# nlohmann::json
# ----------------------
add_subdirectory(third-party/json json-build EXCLUDE_FROM_ALL)
# link the official target
target_link_libraries(${PROJECT_NAME}
  PRIVATE
    nlohmann_json::nlohmann_json
)

# ----------------------
# bit7z
# ----------------------
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/bit7z
  ${CMAKE_BINARY_DIR}/bit7z-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME} PRIVATE bit7z)
target_include_directories(${PROJECT_NAME}
  PRIVATE ${CMAKE_SOURCE_DIR}/third-party/bit7z/lib
)

# ----------------------
# Native File Dialog (nfd)
# ----------------------
add_library(nfd STATIC
  third-party/nfd/src/nfd_common.c
  third-party/nfd/src/nfd_cocoa.m
)
target_include_directories(nfd PUBLIC ${CMAKE_SOURCE_DIR}/third-party/nfd/src/include)
if(APPLE)
  find_library(APPKIT_FRAMEWORK AppKit)
  target_link_libraries(nfd PRIVATE ${APPKIT_FRAMEWORK})
endif()
target_link_libraries(${PROJECT_NAME} PRIVATE nfd)

# ----------------------
# SDL2
# ----------------------
find_package(SDL2 REQUIRED)
target_include_directories(${PROJECT_NAME} PRIVATE ${SDL2_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${SDL2_LIBRARIES})

# ----------------------
# pugixml
# ----------------------
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/pugixml
  ${CMAKE_BINARY_DIR}/pugixml-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME} PRIVATE pugixml::pugixml)
# pugixml::pugixml already carries its include directories

# ----------------------
# libcurl
# ----------------------
find_package(CURL REQUIRED)
target_include_directories(${PROJECT_NAME} PRIVATE ${CURL_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE ${CURL_LIBRARIES})

# ----------------------
# lz4
# ----------------------
add_subdirectory(
  ${CMAKE_SOURCE_DIR}/third-party/lz4/build/cmake
  ${CMAKE_BINARY_DIR}/lz4-build
  EXCLUDE_FROM_ALL
)
target_link_libraries(${PROJECT_NAME} PRIVATE lz4)
target_include_directories(${PROJECT_NAME}
  PRIVATE ${CMAKE_SOURCE_DIR}/third-party/lz4/lib
)
