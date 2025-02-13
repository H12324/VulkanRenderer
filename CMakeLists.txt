cmake_minimum_required(VERSION 3.14)
project(VulkanApp)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

find_package(Vulkan REQUIRED)

# Fetch GLFW and GLM if not found
include(FetchContent)

FetchContent_Declare(
    glfw
    GIT_REPOSITORY https://github.com/glfw/glfw.git
    GIT_TAG latest
)

FetchContent_Declare(
    glm
    GIT_REPOSITORY https://github.com/g-truc/glm.git
    GIT_TAG master
)

FetchContent_MakeAvailable(glfw glm)

# Set platform-specific settings
if (WIN32)
    set(GLFW_BUILD_WIN32 true)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

# Create executable
add_executable(VulkanApp main.cpp)

# Link libraries
target_link_libraries(VulkanApp PRIVATE Vulkan::Vulkan glfw glm)

# Ensure GLFW uses Vulkan
target_compile_definitions(VulkanApp PRIVATE GLFW_INCLUDE_VULKAN)

# Set include directories
target_include_directories(VulkanApp PRIVATE ${glfw_SOURCE_DIR}/include ${glm_SOURCE_DIR})

# Platform-specific settings
if (WIN32)
    target_link_libraries(VulkanApp PRIVATE glfw)
elseif (UNIX)
    target_link_libraries(VulkanApp PRIVATE glfw pthread dl)
endif()
