macro(set_cache_variable VAR_NAME VAR_DESCRIPTION)
    set(${VAR_NAME} ${${VAR_NAME}} CACHE INTERNAL ${VAR_DESCRIPTION})
    message(STATUS "Set ${VAR_NAME} to ${${VAR_NAME}}.")
endmacro()


# CMake modules

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake-modules)


# Code coverage

if(BUILD_CPR_TESTS AND GENERATE_COVERAGE)
    include(CodeCoverage)
endif()


# Curl configuration

if(USE_SYSTEM_CURL)
    find_package(CURL)
endif()
if(NOT USE_SYSTEM_CURL OR NOT CURL_FOUND)
    message(STATUS "Not using system Curl, using built-in curl project instead.")
    add_subdirectory(curl)
    set(CURL_FOUND TRUE)
    set(CURL_LIBRARIES libcurl)
    set(CURL_INCLUDE_DIRS
        ${CURL_SOURCE_DIR}/include
        ${CURL_BINARY_DIR}/include/curl)
endif()

set_cache_variable(CURL_FOUND "Set if libcurl is found or built")
set_cache_variable(CURL_LIBRARIES "Location of libcurl")
set_cache_variable(CURL_INCLUDE_DIRS "Location of curl include files")


# GTest configuration

if(BUILD_CPR_TESTS)
    if(USE_SYSTEM_GTEST)
        find_package(GTest)
    endif()
    if(NOT USE_SYSTEM_GTEST OR NOT GTEST_FOUND)
        message(STATUS "Not using system gtest, using built-in googletest project instead.")
        add_subdirectory(googletest)
        set(GTEST_FOUND TRUE)
        set(GTEST_LIBRARIES gtest)
        set(GTEST_MAIN_LIBRARIES gtest_main)
        set(GTEST_BOTH_LIBRARIES gtest gtest_main)
        set(GTEST_INCLUDE_DIRS ${gtest_SOURCE_DIR}/include)
    endif()

    set_cache_variable(GTEST_FOUND "Set if libgtest was found or built")
    set_cache_variable(GTEST_LIBRARIES "Location of libgtest")
    set_cache_variable(GTEST_MAIN_LIBRARIES "Location of libgtest-main")
    set_cache_variable(GTEST_BOTH_LIBRARIES "Location of both gtest libraries")
    set_cache_variable(GTEST_INCLUDE_DIRS "Location of gtest include files")
endif()


# Mongoose configuration

if(BUILD_CPR_TESTS)
    message(STATUS "Building mongoose project for test support.")
    add_subdirectory(mongoose)
    set(MONGOOSE_FOUND TRUE)
    set(MONGOOSE_LIBRARIES mongoose)
    set(MONGOOSE_INCLUDE_DIRS ${mongoose_SOURCE_DIR})

    set_cache_variable(MONGOOSE_FOUND "Set if libmongoose was found or built")
    set_cache_variable(MONGOOSE_LIBRARIES "Location of libmongoose")
    set_cache_variable(MONGOOSE_INCLUDE_DIRS "Location of mongoose include files")
endif()