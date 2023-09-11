cmake_minimum_required(VERSION 3.21)


project(
    "playhouse_connecter_sample"
    VERSION 1.0.0
    LANGUAGES C CXX)

# Global CMake variables are set here
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Options
option(USE_CONAN "Whether to use Conan." ON)
option(USE_VCPKG "Whether to use VCPKG." OFF)
option(USE_CPM "Whether to use CPM." ON)

option(ENABLE_WARNINGS "Enable to add warnings to a target." ON)
option(ENABLE_WARNINGS_AS_ERRORS "Enable to treat warnings as errors." OFF)

option(ENABLE_TESTING "Enable a Unit Testing build." ON)
option(ENABLE_COVERAGE "Enable a Code Coverage build." OFF)

option(ENABLE_CLANG_TIDY "Enable to add clang tidy." OFF)

option(ENABLE_SANITIZE_ADDR "Enable address sanitize." OFF)
option(ENABLE_SANITIZE_UNDEF "Enable undefined sanitize." OFF)
option(ENABLE_SANITIZE_LEAK "Enable leak sanitize (Gcc/Clang only)." OFF)
option(ENABLE_SANITIZE_THREAD "Enable thread sanitize (Gcc/Clang only)." OFF)

option(ENABLE_CLANG_FORMAT "Enable to add clang-format." ON)
option(ENABLE_CMAKE_FORMAT "Enable to add cmake-format." ON)

option(ENABLE_LTO "Enable to add Link Time Optimization." ON)

# Project/Library Names

set(UNIT_TEST_NAME "unit_tests")
set(EXECUTABLE_NAME "playhouse-connecter-cpp-sample")

# CMAKE MODULES
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake/)
include(ConfigSafeGuards)
include(AddGitSubmodule)
include(Docs)
include(Tools)

if(ENABLE_WARNINGS)
    include(Warnings)
endif()

add_cmake_format_target()
add_clang_format_target()

if(ENABLE_SANITIZE_ADDR OR ENABLE_SANITIZE_UNDEF)
    include(Sanitizer)
    add_sanitizer_flags()
endif()

if(ENABLE_COVERAGE)
    include(CodeCoverage)
    append_coverage_compiler_flags()
endif()

if(ENABLE_LTO)
    include(LTO)
endif()

# EXTERNAL LIBRARIES

if(USE_CONAN)
    message(STATUS "Using Conan")
    include(${CMAKE_BINARY_DIR}/conan_toolchain.cmake)
    find_package(protobuf REQUIRED)
    # find_package(nlohmann_json REQUIRED)
    # find_package(fmt REQUIRED)
    # find_package(spdlog REQUIRED)
    # find_package(Catch2 REQUIRED)
    # find_package(cxxopts REQUIRED)
elseif(USE_VCPKG)
    message(STATUS "Using VCPKG")
    include(${CMAKE_SOURCE_DIR}/external/vcpkg/scripts/buildsystems/vcpkg.cmake)
    find_package(nlohmann_json REQUIRED)
    find_package(fmt REQUIRED)
    find_package(spdlog REQUIRED)
    find_package(Catch2 REQUIRED)
    find_package(cxxopts REQUIRED)
elseif(USE_CPM)
    # message(STATUS "Using CPM")
    # include(CPM)

    # CPMAddPackage(
    #     NAME nlohmann_json
    #     GITHUB_REPOSITORY nlohmann/json
    #     GIT_TAG v3.11.2
    # )


    # CPMAddPackage(
    #     NAME playhouse-connector
    #     GITHUB_REPOSITORY uulala-x/playhouse-connector-cpp
    #     GIT_TAG 99301ce1490206697dfed64cbe22ff3ff0280f
    # )
    # # cpmaddpackage("gh:fmtlib/fmt#9.1.0")
    # cpmaddpackage("gh:nlohmann/json@3.11.2")
    # cpmaddpackage("gh:catchorg/Catch2@2.13.9")
    # cpmaddpackage("gh:jarro2783/cxxopts@3.1.1")
    # cpmaddpackage("gh:gabime/spdlog@1.11.0")
endif()

message(STATUS "Using CPM")
include(CPM)

CPMAddPackage(
    NAME playhouse_connector
    GITHUB_REPOSITORY ulala-x/playhouse-connector-cpp
    GIT_TAG d8654e8a51ce2f7495c8399953ececbdbf91c705
)

# SUB DIRECTORIES

add_subdirectory(configured)
add_subdirectory(external)
add_subdirectory(app)
add_subdirectory(tests)

# INSTALL TARGETS

install(
    TARGETS ${EXECUTABLE_NAME}
    EXPORT ${LIBRARY_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin)

install(
    TARGETS ${LIBRARY_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib)
