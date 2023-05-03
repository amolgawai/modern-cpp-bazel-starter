cmake_minimum_required(VERSION 3.23 FATAL_ERROR)

# ---- Options ----

# option(ENABLE_CODE_COVERAGE "Enable test coverage" OFF)
option(TEST_INSTALLED_VERSION "Test the version found by find_package" OFF)

# ---- Create binary ----

file(GLOB sources CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
add_executable(greeter_tests ${sources})
target_link_libraries(greeter_tests doctest::doctest greeter::greeter)
target_compile_features(greeter_tests PRIVATE cxx_std_17)
target_set_warnings(
    greeter_tests
    ENABLE
    ALL
    AS_ERROR
    ALL
    DISABLE
    Annoying
)

# enable compiler warnings if(NOT TEST_INSTALLED_VERSION) if(CMAKE_CXX_COMPILER_ID MATCHES "Clang" OR
# CMAKE_CXX_COMPILER_ID MATCHES "GNU") target_compile_options(greeter PUBLIC -Wall -Wpedantic -Wextra -Werror)
# elseif(MSVC) target_compile_options(greeter PUBLIC /W4 /WX) target_compile_definitions(greeter_tests PUBLIC
# DOCTEST_CONFIG_USE_STD_HEADERS) endif() endif()

# ---- Add GreeterTests ----

add_test(greeter_tests greeter_tests)

# Note: doctest and similar testing frameworks can automatically configure CMake tests. For other testing frameworks add
# the tests target instead: add_test(NAME ${PROJECT_NAME} COMMAND ${PROJECT_NAME})

include(${doctest_SOURCE_DIR}/scripts/cmake/doctest.cmake)
doctest_discover_tests(greeter_tests)

# ---- code coverage ----

if(ENABLE_CODE_COVERAGE)
    setup_target_for_coverage(
        coverage_greeter # Name for custom target.
        greeter_tests # Name of the test driver executable that runs the tests. NOTE! This should always have a ZERO as
                      # exit code otherwise the coverage generation will not complete.
        coverage_out # Name of output directory.
    )
endif()
