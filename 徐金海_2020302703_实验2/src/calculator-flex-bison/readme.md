# 计算器

## 主要功能

选项-a可产生抽象语法树AST

选项-r可产生线性IR

选项-R可直接运行得出计算器结果

## 源代码构成

calculator.l 借助flex工具实现的词法分析器脚本源代码

calculator_lex.cpp 借助flex工具实现的词法分析器自动生成C语言源文件

calculator_lex.h 借助flex工具实现的词法分析器自动生成C语言头文件

calculator.y 借助bison工具实现的语法分析器脚本源代码

calculator_yacc.cpp 借助bison工具实现的语法分析器自动生成C语言源文件

calculator_yacc.h 借助bison工具实现的语法分析器自动生成C语言头文件

expr.cpp 遍历抽象语法树进行表达式运算相关函数

expr.h expr.c对应的头文件

ast.cpp 抽象语法树创建所需要的函数

ast.h ast.cpp对应的头文件

graph.cpp 遍历抽象语法树利用graphviz生成图片

graph.h graph.cpp对应的头文件

symbol.cpp 符号表管理

symbol.h symbol.cpp对应的头文件

genIR.cpp 遍历抽象语法树产生线性IR

IRInst.cpp 线性IR指令类的实现

IRInst.h 线性IR指令类的头文件

IRCode.cpp 线性IR管理类的实现

IRCode.h 线性IR管理类的头文件

main.cpp 计算器程序的主函数

logo.ico 计算器程序的图标

resouce.rc.in Windows系统资源文件，要为GB2312编码或者GBK编码

test.txt 测试用例代码

## 计算器使用方法

### Linux平台

./cmake-build-debug/calculator-flex-bison -a test.txt

./cmake-build-debug/calculator-flex-bison -a -o test.pdf test.txt

./cmake-build-debug/calculator-flex-bison -r test.txt

./cmake-build-debug/calculator-flex-bison -r -o ir.txt test.txt

./cmake-build-debug/calculator-flex-bison -R test.txt

### Windows平台
cmake -S . -B cmake-build-debug -DCMAKE_BUILD_TYPE=Debug
cmake --build cmake-build-debug --parallel

.\cmake-build-debug\calculator-flex-bison.exe -a test.txt

.\cmake-build-debug\calculator-flex-bison.exe -a -o test.pdf test.txt

.\cmake-build-debug\calculator-flex-bison.exe -r test.txt

.\cmake-build-debug\calculator-flex-bison.exe -r -o ir.txt test.txt

.\cmake-build-debug\calculator-flex-bison.exe -R test.txt

## 工具安装

### flex&bison

#### Windows(MinGW)

pacman -S flex bison

#### Linux(ubuntu)

sudo apt install flex bison

### graphviz

#### Windows(MinGW)

pacman -S pkg-config mingw-w64-x86_64-graphviz

#### Linux(ubuntu)

sudo apt install pkg-config graphviz libgraphviz-dev

## flex与bison的使用方法

### Windows

主要给Visual Studio用

win_flex -o calculator_lex.cpp --wincompat calculator.l

win_bison -o calculator.cpp calculator.y

### MinGW、Linux or Mac

flex -o calculator_lex.cpp --header-file=calculator_lex.h calculator.l

bison -o calculator_yacc.cpp --header=calculator_yacc.h -d calculator.y

请注意bison的--header在某些平台上可能是--defines，要根据情况调整指定。

## 计算器强化

在CPU中，整数与实数不能直接进行数学运算，整数靠ALU进行运算，实数靠浮点协处理器或者数学库进行运算，
所以整数需要先转换成实数，然后做实数运算。

因此，对于线性IR指令，必须区分整数运算与实数运算。例如加法，IR指令必须区分为整数加法和实数加法。
还需要追加整数转换为实数的指令，需要时追加实数转整数的指令。
