cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

# project(GreeterStandalone LANGUAGES CXX)

# CPMAddPackage(NAME Greeter SOURCE_DIR ${CMAKE_CURRENT_LIST_DIR}/..)

# ---- Create standalone executable ----

file(GLOB sources CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)

add_executable(IntroApp ${sources})

target_compile_features(greeter PRIVATE cxx_std_17)
set_target_properties(IntroApp PROPERTIES OUTPUT_NAME "IntroApp")
target_set_warnings(
    IntroApp
    ENABLE
    ALL
    AS_ERROR
    ALL
    DISABLE
    Annoying
) # Set warnings (if needed).

target_link_libraries(IntroApp greeter cxxopts)
