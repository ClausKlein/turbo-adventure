# SPDX-License-Identifier: Apache-2.0 WITH LLVM-exception

find_package(GTest REQUIRED)

add_executable(beman.my_project_name.tests.todo)
target_sources(beman.my_project_name.tests.todo PRIVATE todo.test.cpp)
target_link_libraries(
    beman.my_project_name.tests.todo
    PRIVATE beman::my_project_name GTest::gtest_main
)
if(BEMAN_EXEMPLAR_USE_MODULES)
    set_target_properties(
        beman.my_project_name.tests.todo
        PROPERTIES CXX_MODULE_STD ON
    )
endif()

include(GoogleTest)
gtest_discover_tests(beman.my_project_name.tests.todo DISCOVERY_TIMEOUT 60)

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    return()
endif()

#======================================================================
# NOTE: Only for Release build with enabled install rules useful! CK
#======================================================================
if(
    BEMAN_MY_PROJECT_NAME_INSTALL_CONFIG_FILE_PACKAGE
    AND NOT CMAKE_SKIP_INSTALL_RULES
)
    # test if the targets are usable from the install directory
    add_test(
        NAME install-to-stagedir
        COMMAND
            ${CMAKE_COMMAND} --install ${CMAKE_BINARY_DIR} --prefix
            ${CMAKE_BINARY_DIR}/stagedir --config $<CONFIG>
    )
    add_test(
        NAME find-package-test
        COMMAND
            ${CMAKE_CTEST_COMMAND} # --verbose
            --output-on-failure -C $<CONFIG> #
            --build-and-test "${CMAKE_SOURCE_DIR}/examples"
            "${CMAKE_CURRENT_BINARY_DIR}/find-package-test" #
            --build-generator ${CMAKE_GENERATOR} #
            --build-makeprogram ${CMAKE_MAKE_PROGRAM} #
            --build-options #
            "-D BEMAN_BUILDSYS_SANITIZER=${BEMAN_BUILDSYS_SANITIZER}"
            "-D BEMAN_USE_MODULES=${BEMAN_USE_MODULES}"
            "-D BEMAN_USE_STD_MODULE=${BEMAN_USE_STD_MODULE}"
            "-D BEMAN_MY_PROJECT_NAME_USE_MODULES=${BEMAN_MY_PROJECT_NAME_USE_MODULES}"
            "-D CMAKE_BUILD_TYPE=$<CONFIG>"
            "-D CMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}"
            "-D CMAKE_CXX_EXTENSIONS=${CMAKE_CXX_EXTENSIONS}"
            "-D CMAKE_CXX_MODULE_STD=${CMAKE_CXX_MODULE_STD}"
            "-D CMAKE_CXX_STANDARD=${CMAKE_CXX_STANDARD}"
            "-D CMAKE_CXX_STANDARD_REQUIRED=${CMAKE_CXX_STANDARD_REQUIRED}"
            "-D CMAKE_PREFIX_PATH=${CMAKE_BINARY_DIR}/stagedir"
            "-D CMAKE_TOOLCHAIN_FILE=${CMAKE_TOOLCHAIN_FILE}"
    )
endif()
