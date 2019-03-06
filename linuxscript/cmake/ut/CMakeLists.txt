message("build ut testcases...")

# virtual project for common include and library file path.
project(ut)

set(PROJECT_DIR "${PROJECT_SOURCE_DIR}/../..")

#add python lib and include
set(Python_ADDITIONAL_VERSIONS 3.7.1)
find_package (Python3 COMPONENTS Interpreter Development)

# for all ut executables;
include_directories(${Python3_INCLUDE_DIRS})
include_directories(${PROJECT_DIR}/build/thirdparty/gtest/include)
include_directories(${PROJECT_DIR}/build/thirdparty/pybind11/include)
include_directories(${PROJECT_DIR}/include)
include_directories(${PROJECT_DIR}/src/ir)
include_directories(${PROJECT_DIR}/src/utils)
include_directories(${PROJECT_DIR}/src/parse)
include_directories(${PROJECT_DIR}/src/infer)
include_directories(${PROJECT_DIR}/src/operators)
include_directories(${PROJECT_DIR}/tests/ut)

link_directories(${PROJECT_DIR}/build/thirdparty/glog/lib)
link_directories(${PROJECT_DIR}/build/thirdparty/gtest/lib)
link_directories(${PROJECT_DIR}/build/me/src)

aux_source_directory(. UT_COMMON_SRCS)
set(UT_SRCS ${UT_COMMON_SRCS})
set(ME_SRCS)

function(add_sub_testsuite ut_srcs me_srcs item)
    # add ut srcs
    aux_source_directory(${item} UT_ITEM_SRCS)
    list(APPEND ${ut_srcs} ${UT_ITEM_SRCS})
    set(${ut_srcs} ${${ut_srcs}} PARENT_SCOPE)

    if (ENABLE_COVERAGE)
        # add me srcs
        aux_source_directory(${PROJECT_DIR}/src/${item} ME_ITEM_SRCS)
        list(APPEND ${me_srcs} ${ME_ITEM_SRCS})
        set(${me_srcs} ${${me_srcs}} PARENT_SCOPE)
    endif()
endfunction()

# add ut suites
add_sub_testsuite(UT_SRCS ME_SRCS ir)
add_sub_testsuite(UT_SRCS ME_SRCS parser)
add_sub_testsuite(UT_SRCS ME_SRCS operators)
add_sub_testsuite(UT_SRCS ME_SRCS infer)
add_sub_testsuite(UT_SRCS ME_SRCS abstract)
add_sub_testsuite(UT_SRCS ME_SRCS utils)
add_sub_testsuite(UT_SRCS ME_SRCS prim)

add_executable(ut_tests ${UT_SRCS} ${ME_SRCS})

if (ENABLE_COVERAGE)
    SETUP_TARGET_FOR_COVERAGE_LCOV(NAME ut_tests_coverage
        EXECUTABLE ut_tests
        DEPENDENCIES ut_tests)
endif()

target_link_libraries(ut_tests gtest gmock_main mindexpr)
