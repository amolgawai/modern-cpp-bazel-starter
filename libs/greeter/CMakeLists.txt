cmake_minimum_required(VERSION 3.23 FATAL_ERROR)

if(Doxygen_FOUND)
    add_subdirectory(docs)
else()
    message(STATUS "Doxygen not found, not building docs")
endif()

# ---- Add source files ----

# Note: globbing sources is considered bad practice as CMake's generators may not detect new files automatically. Keep
# that in mind when changing files, or explicitly mention them here.
file(GLOB_RECURSE headers CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/include/greeter/*.h")
file(GLOB_RECURSE sources CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

# ---- Create library ----

# Note: for header-only libraries change all PUBLIC flags to INTERFACE and create an interface target:
# add_library(${PROJECT_NAME} INTERFACE)
add_library(greeter ${headers} ${sources})

# All users of this library will need at least C++17
target_compile_features(greeter PUBLIC cxx_std_17)

target_set_warnings(
    greeter
    ENABLE
    ALL
    AS_ERROR
    ALL
    DISABLE
    Annoying
) # Set warnings (if needed).

# being a cross-platform target, we enforce standards conformance on MSVC
target_compile_options(greeter PUBLIC "$<$<COMPILE_LANG_AND_ID:CXX,MSVC>:/permissive->")

# Link dependencies
target_link_libraries(greeter PRIVATE fmt::fmt)

target_include_directories(
    greeter PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                   $<INSTALL_INTERFACE:include/${PROJECT_NAME}-${PROJECT_VERSION}>
)

if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME AND BUILD_TESTING)
    add_subdirectory(tests)
endif()
