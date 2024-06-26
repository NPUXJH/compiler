cmake_minimum_required(VERSION 3.13)

# 设置工程名，也就是可执行程序的名字，不含后缀
set(PROJECT_NAME calculator-flex-bison)

# 设置工程属性，如版本，开发语言等
project(${PROJECT_NAME} VERSION 1.0.0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

SET(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# 查找flex工具，找不到则出错；FLEX_EXECUTABLE记录flex程序的位置
#find_program(FLEX_EXECUTABLE NAMES flex REQUIRED)
# 如果flex查找不到，注释掉上一行的find_program，解开下面任任一行，要么设置MSYS64_BIN环境变量，要么给定flex.exe所在文件夹，如下所示
#find_program(FLEX_EXECUTABLE NAMES flex PATHS ENV MSYS64_BIN REQUIRED)
find_program(FLEX_EXECUTABLE NAMES flex PATHS "D:/CompilerTools/msys64/usr/bin" REQUIRED)

message(STATUS ${FLEX_EXECUTABLE})

# 查找bison工具，找不到则出错；BISON_EXECUTABLE记录bison程序的位置
#find_program(BISON_EXECUTABLE NAMES bison REQUIRED)
# 如果bison查找不到，注释掉上一行的find_program，解开下面任任一行，要么设置MSYS64_BIN环境变量，要么给定flex.exe所在文件夹，如下所示
#find_program(BISON_EXECUTABLE NAMES bison PATHS ENV MSYS64_BIN REQUIRED)
find_program(BISON_EXECUTABLE NAMES bison PATHS "D:/CompilerTools/msys64/usr/bin" REQUIRED)

message(STATUS ${BISON_EXECUTABLE})

# 查找graphviz库是否在系统中安装，若安装则设置一系列的变量，如LIBGVC_INCLUDE_DIRS等
find_package(Graphviz REQUIRED)

# 配置创建一个可执行程序，以及该程序所依赖的所有源文件、头文件等
add_executable(${PROJECT_NAME}
    calculator_lex.cpp
    calculator_lex.h
    calculator_yacc.cpp
    calculator_yacc.h
    lexer.h
    parser.h
    ast.cpp
    ast.h
    expr.cpp
    expr.h
    main.cpp
    graph.cpp
    graph.h
    common.cpp
    common.h
    symbol.cpp
    symbol.h
    IRInst.h
    IRInst.cpp
    IRCode.h
    IRCode.cpp
    genIR.cpp
)

# 设置语言标准，可根据需要调整
set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 17
    CXX_EXTENSIONS OFF
    CXX_STANDARD_REQUIRED ON
)

# -Wno-unused-function避免无用函数警告
# -Wno-write-strings避免c++提示字符串常量转换char*的警告
# -Werror强制警告当作错误处理
# -Wall尽可能多的让编译器提示警告和错误
target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Werror -Wno-write-strings -Wno-unused-function)

# 引入graphviz库的头文件，防止编译时找不到graphviz的头文件
target_include_directories(${PROJECT_NAME} PRIVATE ${Graphviz_INCLUDE_DIRS})

# 指定预处理宏，flex和bison两个工具都用
target_compile_definitions(${PROJECT_NAME} PRIVATE FLEX_USE BISON_USE)

# 指定graphviz的库文件以及位置，防止链接时找不到graphviz的库函数
target_link_libraries(${PROJECT_NAME}  PRIVATE ${Graphviz_LIBRARIES})

# 通过flex产生词法分析源代码
add_custom_command(OUTPUT
    ${CMAKE_CURRENT_SOURCE_DIR}/calculator_lex.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/calculator_lex.h
    PRE_BUILD
    COMMAND
    ${FLEX_EXECUTABLE} -o ${CMAKE_CURRENT_SOURCE_DIR}/calculator_lex.cpp --header-file=${CMAKE_CURRENT_SOURCE_DIR}/calculator_lex.h ${CMAKE_CURRENT_SOURCE_DIR}/calculator.l
    DEPENDS
    calculator.l
)

# Window版和Linux版产生头文件时选项名字不一样，根据不同系统设置
if(CMAKE_HOST_WIN32)
    set(BISON_HEADFILE_OPTION "--header")
else()
    set(BISON_HEADFILE_OPTION "--defines")
endif()

# 通过bison生成语法分析源代码
add_custom_command(OUTPUT
    ${CMAKE_CURRENT_SOURCE_DIR}/calculator_yacc.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/calculator_yacc.h
    PRE_BUILD
    COMMAND
    ${BISON_EXECUTABLE} -d -o ${CMAKE_CURRENT_SOURCE_DIR}/calculator_yacc.cpp ${BISON_HEADFILE_OPTION}=${CMAKE_CURRENT_SOURCE_DIR}/calculator_yacc.h ${CMAKE_CURRENT_SOURCE_DIR}/calculator.y
    DEPENDS
    calculator.y
)
