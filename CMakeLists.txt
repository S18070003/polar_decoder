# CMake entry point
cmake_minimum_required (VERSION 3.0.2)
cmake_policy(SET CMP0054 NEW)

project (polar_decoder_gen)

# Enable C++11
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang" OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "AppleClang")
    set (CMAKE_CXX_STANDARD 14)
else ()
    set (CMAKE_CXX_STANDARD 11)
endif()

# Specify bin path
set (EXECUTABLE_OUTPUT_PATH bin/)

# Generate the source files list
file (GLOB_RECURSE source_files src/*)

set (AFF3CT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/cmake-config/")
find_package (AFF3CT CONFIG REQUIRED)
if (AFF3CT_FOUND)
    if (NOT AFF3CT_SYSTEMC_MODULE AND (AFF3CT_PREC STREQUAL "MULTI" OR AFF3CT_PREC STREQUAL "32"))
        message(STATUS "AFF3CT - Library found")

        # Add the compile option from AFF3CT
        add_compile_options(${AFF3CT_COMPILE_OPTIONS})

        # Add the definitions of AFF3CT
        add_definitions(${AFF3CT_DEFINITIONS})

        # Add the include directories from AFF3CT
        include_directories(${AFF3CT_INCLUDE_DIRS})

        # Define the objects from the source files
        add_library (polar_decoder_gen-obj OBJECT ${source_files})

        # Declare the binary
        add_executable        (polar_decoder_gen-bin $<TARGET_OBJECTS:polar_decoder_gen-obj>)
        set_target_properties (polar_decoder_gen-bin PROPERTIES OUTPUT_NAME polar_decoder_gen)

        # Link with AFF3CT
        target_link_libraries(polar_decoder_gen-bin ${AFF3CT_LIBRARIES})
    else()
        message(FATAL_ERROR "Incompatible AFF3CT library build.")
    endif()
endif (AFF3CT_FOUND)
