cmake_minimum_required(VERSION 3.16)
project(Triangle VERSION 1.0 LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# --- glad2 automatisch herunterladen und generieren ---
include(FetchContent)
FetchContent_Declare(
    glad
    GIT_REPOSITORY https://github.com/Dav1dde/glad
    GIT_TAG v2.0.6
    GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(glad)
add_subdirectory("${glad_SOURCE_DIR}/cmake" glad_cmake)
glad_add_library(glad_gl_core_33 REPRODUCIBLE API gl:core=3.3)

# --- Eigene Sourcen (ohne die alte src/gl.c, die kommt jetzt von glad) ---
set(SOURCE_FILES
    src/main.cpp
)

add_executable(${PROJECT_NAME} ${SOURCE_FILES})

target_include_directories(${PROJECT_NAME}
PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

find_package(OpenGL REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(GLFW REQUIRED glfw3)

target_include_directories(${PROJECT_NAME}
PRIVATE
    ${GLFW_INCLUDE_DIRS}
)

target_link_libraries(${PROJECT_NAME}
PRIVATE
    OpenGL::GL
    ${GLFW_LIBRARIES}
    glad_gl_core_33
)

target_compile_options(${PROJECT_NAME}
PRIVATE
    $<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:-Wall;-Wextra;-Wpedantic>
)