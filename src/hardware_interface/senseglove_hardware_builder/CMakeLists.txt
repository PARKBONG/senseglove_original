cmake_minimum_required(VERSION 3.0.2)
project(senseglove_hardware_builder)

add_compile_options(-std=c++14 -Wall -Wextra -Werror)

find_package(catkin REQUIRED COMPONENTS
        roscpp
        roslib
        senseglove_hardware
        urdf
        )

set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

find_library(SGCORE
    NAMES libSGCoreCpp.so
    PATHS ../../SenseGlove_API/Core/SGCoreCpp/lib/linux/Release
    REQUIRED)

find_library(SGCONNECT
    NAMES libSGConnect.so
    PATHS ../../SenseGlove_API/Core/SGConnect/lib/linux
    REQUIRED)

catkin_package(
        INCLUDE_DIRS include
        CATKIN_DEPENDS
        roscpp
        roslib
        senseglove_hardware
        urdf
        LIBRARIES ${PROJECT_NAME}
)

#if(CATKIN_ENABLE_TESTING AND ENABLE_COVERAGE_TESTING)
#    find_package(code_coverage REQUIRED)
#    # Add compiler flags for coverage instrumentation before defining any targets
#    append_coverage_compiler_flags()
#endif()

include_directories(include
        SYSTEM
        ${catkin_INCLUDE_DIRS}
        ../../SenseGlove_API/Core/SGConnect/incl
        ../../SenseGlove_API/Core/SGCoreCpp/incl)

add_library(${PROJECT_NAME}
        src/hardware_builder.cpp
        )

target_link_libraries(${PROJECT_NAME}
        ${catkin_LIBRARIES}
        ${SGCORE}
        ${SGCONNECT}
        yaml-cpp
        )

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
        )

install(DIRECTORY robots
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
        )

install(TARGETS ${PROJECT_NAME}
        ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        )

## Add gtest based cpp test target and link libraries
#if(CATKIN_ENABLE_TESTING)
#    catkin_add_gtest(${PROJECT_NAME}_test
#            test/absolute_encoder_builder_test.cpp
#            )
#    target_link_libraries(${PROJECT_NAME}_test ${catkin_LIBRARIES} ${PROJECT_NAME})
#
#    if(ENABLE_COVERAGE_TESTING)
#        set(COVERAGE_EXCLUDES "*/${PROJECT_NAME}/test/*")
#        add_code_coverage(
#                NAME coverage_report
#                DEPENDENCIES ${PROJECT_NAME}_test
#        )
#    endif()
#endif()
