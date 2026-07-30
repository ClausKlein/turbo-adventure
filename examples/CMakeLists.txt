# SPDX-License-Identifier: Apache-2.0 WITH LLVM-exception
cmake_minimum_required(VERSION 3.30...4.4)

include(../infra/cmake/enable-experimental-import-std.cmake)

project(beman.my_project_name.examples LANGUAGES CXX)

if(PROJECT_IS_TOP_LEVEL)
    find_package(beman.my_project_name 0.1.1 EXACT REQUIRED)

    enable_testing()
endif()

set(ALL_EXAMPLES todo)
message("Examples to be built: ${ALL_EXAMPLES}")

foreach(example ${ALL_EXAMPLES})
    add_executable(beman.my_project_name.examples.${example})
    target_sources(
        beman.my_project_name.examples.${example}
        PRIVATE ${example}.cpp
    )
    target_link_libraries(
        beman.my_project_name.examples.${example}
        PRIVATE beman::my_project_name
    )
    if(BEMAN_MY_PROJECT_NAME_USE_MODULES)
        set_target_properties(
            beman.my_project_name.examples.${example}
            PROPERTIES CXX_MODULE_STD ON
        )
    endif()
    add_test(
        NAME beman.my_project_name.examples.${example}
        COMMAND beman.my_project_name.examples.${example}
    )
endforeach()
