cmake_minimum_required(VERSION 3.26)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_COMPILER_WORKS 1)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP /EHsc")
include(FetchContent)

# Safetyhook
set(SAFETYHOOK_FETCH_ZYDIS ON)
include(FetchContent)
FetchContent_Declare(
        safetyhook
        GIT_REPOSITORY "https://github.com/cursey/safetyhook.git"
        GIT_TAG "b046e123dc69821f2c375161e0adef3c6d9c9db4"
)
FetchContent_MakeAvailable(safetyhook)


# OpenXR
FetchContent_Declare(
        openxr
        GIT_REPOSITORY "https://github.com/KhronosGroup/OpenXR-SDK"
        GIT_TAG "b7ada0bdecd9830f27c2221dad6f0bb933c64f15"
)
FetchContent_MakeAvailable(openxr)

# BF2VR
project(BF2VR)
add_library(
        BF2VR MODULE
        "src/BF2VR/main.cpp"
        "src/BF2VR/Engine/BF2Service.cpp"
        "src/BF2VR/Rendering/D3DService.cpp"
        "src/BF2VR/Utils/Logging.cpp"
        "src/BF2VR/VR/ActionsService.cpp"
        "src/BF2VR/VR/OpenXRService.cpp"
)
target_include_directories(
        BF2VR PRIVATE
        "src/BF2VR"
        "src/BF2VR/Engine"
        "src/BF2VR/Rendering"
        "src/BF2VR/Utils"
        "src/BF2VR/VR"
        "${OPENXR_SOURCE_DIR}/include"
        "${safetyhook_SOURCE_DIR}/include"
)
target_link_libraries(
        BF2VR PRIVATE
        "openxr_loader"
        "safetyhook"
        "d3d11"
        "d3dcompiler"
)
set_target_properties(BF2VR PROPERTIES PREFIX "")

# Loader
project(Loader)
add_executable(Loader
        src/Loader/main.cpp
        src/Loader/Loader.rc
)
target_link_libraries(Loader PRIVATE)
