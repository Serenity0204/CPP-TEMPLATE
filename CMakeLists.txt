cmake_minimum_required(VERSION 3.14 FATAL_ERROR) #FetchContent_MakeAvailable
project(ProjectName)

# flag to turn on/off test options
option(BUILD_TESTS "Build tests" ON)




#- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
#   Grab all cpp files from includes folder
#- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
set(INCLUDES_FOLDER includes)
FILE(GLOB_RECURSE SOURCE_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} CONFIGURE_DEPENDS "${INCLUDES_FOLDER}/*.cpp")
#- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 






## for testing
if(BUILD_TESTS)
    # install gtest
    find_package(GTest QUIET)
    if (NOT ${GTest_FOUND}) # GTest not found, FetchContent!
        include(FetchContent)
            FetchContent_Declare(
                googletest
                GIT_REPOSITORY https://github.com/google/googletest.git
                GIT_TAG        v1.13.0)
            FetchContent_MakeAvailable(googletest)
    endif()
    # GoogleTest requires at least C++14
    set(CMAKE_CXX_STANDARD 14)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)

    ADD_EXECUTABLE(main
        main.cpp
        ${SOURCE_FILES}
    )
    ADD_EXECUTABLE(test_stub
        tests/test_stub.cpp
        ${SOURCE_FILES}
    )
    # link
    TARGET_LINK_LIBRARIES(main GTest::gtest_main)
    TARGET_LINK_LIBRARIES(test_stub GTest::gtest_main)
endif() 



## -----------------------------------------------------------------------------

## for HEADER ONLY library project

# # Adding interface library (header-only)
# add_library(ProjectName INTERFACE)

# # Specify include directories
# target_include_directories(ProjectName INTERFACE 
#     $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/includes>
#     $<INSTALL_INTERFACE:include>  # <prefix>/include
# )

# # Install library target
# install(TARGETS ProjectName
#     EXPORT ProjectNameTargets
#     INCLUDES DESTINATION include
# )

# # This makes project importable from the install directory
# # Put config file into the CMake user package registry
# install(EXPORT ProjectNameTargets
#     FILE ProjectNameTargets.cmake
#     NAMESPACE ProjectName::
#     DESTINATION lib/cmake/ProjectName
# )

# # This makes project importable from the build directory
# export(TARGETS ProjectName
#     NAMESPACE ProjectName::
#     FILE ${CMAKE_CURRENT_BINARY_DIR}/ProjectNameTargets.cmake
# )


## -----------------------------------------------------------------------------

## for library with DEPENDENCIES
## TBD