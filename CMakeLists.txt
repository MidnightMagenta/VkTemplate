cmake_minimum_required(VERSION 3.10)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set(ProjectName "VK_Template")

project("${ProjectName}" VERSION 0.1)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /EHsc")

if (NOT DEFINED ENV{VULKAN_SDK})
    message(FATAL_ERROR "VULKAN_SDK is not set.")
endif()

add_subdirectory("${CMAKE_SOURCE_DIR}/dependencies/vma")

add_custom_target(compile_shaders ALL
    COMMENT "Compiling shaders"
    COMMAND "${PROJECT_SOURCE_DIR}/shaders/compile.bat"
    WORKING_DIRECTORY "${PROJECT_SOURCE_DIR}/shaders"

    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/shaders/shader_bin ${CMAKE_BINARY_DIR}/shaders
)

add_custom_target(q_start_clock ALL
    COMMENT "Starting console clock"
    COMMAND "${PROJECT_SOURCE_DIR}/console_clock/bin/run.bat"
    WORKING_DIRECTORY "${PROJECT_SOURCE_DIR}/console_clock/bin"
)

#app executable
add_executable("${ProjectName}"
        #src files
        src/main.cpp
        
        #header files
        include/main_header.hpp
)

add_dependencies("${ProjectName}" compile_shaders q_start_clock)

target_include_directories("${ProjectName}" PUBLIC 
    "${CMAKE_SOURCE_DIR}/dependencies/glm" 
    "C:/VulkanSDK/1.3.296.0/Include"
)

target_link_libraries("${ProjectName}" PUBLIC 
    "$ENV{VULKAN_SDK}/Lib/vulkan-1.lib"
)