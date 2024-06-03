cmake_minimum_required(VERSION 3.1.0)

set(PROJECT_NAME nanovg)

project(${PROJECT_NAME})
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_C_STANDARD 11)

set(SRC_DIR "./src")

if(NANOVG_METAL_IMPLEMENTATION)
file(GLOB SRCS ${SRC_DIR}/*.cpp ${SRC_DIR}/*.m)
else()
file(GLOB SRCS ${SRC_DIR}/*.cpp)
endif()

file(GLOB HDRS ${SRC_DIR}/*.h)

add_library(${PROJECT_NAME} ${SRCS})

if(NANOVG_METAL_IMPLEMENTATION)
target_compile_options(nanovg PRIVATE "-fobjc-arc")
set_target_properties(
    nanovg
    PROPERTIES
    XCODE_ATTRIBUTE_CLANG_ENABLE_OBJC_ARC YES
)
endif()

install(TARGETS ${PROJECT_NAME}
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include/${PROJECT_NAME}
)


install(FILES ${HDRS} DESTINATION include/${PROJECT_NAME}/)

find_package(OpenGL REQUIRED)
add_subdirectory(glfw)
add_executable(example_gl3 example/example_gl3.c example/demo.c example/perf.c)
target_link_libraries(example_gl3 nanovg glfw ${OPENGL_LIBRARIES})
target_include_directories(example_gl3 PUBLIC src ${OPENGL_INCLUDE_DIR})
