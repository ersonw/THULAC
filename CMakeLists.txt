# 指定 CMake 最低版本
cmake_minimum_required(VERSION 3.10)

# 设置项目名称和版本
project(thulac VERSION 1.0 LANGUAGES CXX)

# 定义目录
set(INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
set(SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(BIN_DIR ${CMAKE_CURRENT_BINARY_DIR})  # 使用构建目录
set(TEST_DIR ${CMAKE_CURRENT_SOURCE_DIR}/test)

# 设置 C++ 标准和编译选项
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -march=native")

# 包含头文件目录
include_directories(${INCLUDE_DIR})



# 定义库文件
if(UNIX OR (CMAKE_SYSTEM_NAME STREQUAL "Windows" AND CMAKE_CXX_COMPILER_ID STREQUAL "GNU"))
    add_library(thulac_static STATIC ${SRC_DIR}/thulac_so.cc ${INCLUDE_DIR}/thulac_lib.cc)
    add_library(thulac_shared SHARED ${SRC_DIR}/thulac_so.cc ${INCLUDE_DIR}/thulac_lib.cc)
    set_target_properties(thulac_shared PROPERTIES
            OUTPUT_NAME "thulac"
            VERSION "1.0"
            SOVERSION "1"
            POSITION_INDEPENDENT_CODE ON
    )
    set_target_properties(thulac_shared PROPERTIES
            INSTALL_NAME_DIR ${BIN_DIR}
    )
    install(TARGETS thulac_shared DESTINATION ${BIN_DIR})
endif()

# 添加可执行文件
add_executable(thulac ${SRC_DIR}/thulac.cc)
target_link_libraries(thulac thulac_static)
add_executable(train_c ${SRC_DIR}/train_c.cc)
target_link_libraries(train_c thulac_static)
add_executable(test_case ${TEST_DIR}/test_case.cpp)
target_link_libraries(test_case thulac_static)
add_executable(thulac_test ${SRC_DIR}/thulac_test.cc)
target_link_libraries(thulac_test thulac_static)