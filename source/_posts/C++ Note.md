---
layout:     post
title:      Note for C++
category:   Linux
tags:       C++
---



## 1. ifndef

头件的中的#ifndef，这是一个很关键的东西。比如你有两个C文件，这两个C文件都include了同一个头文件。而编译时，这两个C文件要一同编译成一个可运行文件，于是问题来了，大量的声明冲突。

```C++
#ifndef <标识>
#define <标识>

......
......
#endif
```

标识的命名有规定：每个头文件的命名应该是唯一的，标识的命名规则是头文件名字的大写，前后加下划线，文件名中的 . 也变成下划线

<!--more-->

## 2. extern

### 2.1 extern 做变量声明

extern 声明的全局变量和函数可以实现跨文件访问

一般把所有全局变量和全局函数的实现都放在一个 `*.cpp` 文件里面，然后用一个同名的 `*.h` 文件包含所有的函数和变量的声明。

```c++
//Demo.h
#pargma once
extern inta;
extern intb;
int add(inta,intb);
```

```c++
//Demo.cpp
int a = 10;
int b = 20;
int add(intm,intn)
{
    return intm+intn;
}
```

## 3. std::deque

std::deque （ double-ended queue ，双端队列）是有下标顺序容器，它允许在其首尾两段快速插入及删除。

## 4. Make

代码变为可执行文件的过程叫编译(complie)，对编译的安排就是构建(build)。

### 4.1 Make 命令

make 是一个根据指定的 Shell 命令进行构建的工具。

### 4.2 Makefile 文件的格式

Makefile 由一系列规则组成，形式如下：

```xml
<target>:<prerequisites>
[tab]<commands>
```

#### 4.2.1 目标 (target)

一个目标就是是一个规则，目标通常是文件名，指明 Make 命令所有构建的对象。目标可以是多个文件名，中间用空格分隔。

目标可以是某个操作的名字，称为“伪目标” (phony target)。

```makefile
.PHONY:clean
clean:
    rm *.o
```

执行伪目标时要明确声明，因为如果当前目录中，正好有一个文件叫做clean，那么这个命令不会执行。因为Make发现clean文件已经存在，就认为没有必要重新构建了，就不会执行指定的rm命令。

`make` 不加参数时默认执行Makefile文件的第一个目标。

#### 4.2.2 前置条件 (prerequisites)

前置条件是一组文件名，之间用空格分隔。它指定了“目标”是否重新构建的标准：只要有一个前置文件不存在，或者有过更新（以时间戳为准），“目标“就需要重新构建。

```makefile
result.txt: source.txt
    cp source.txt result.txt
```

构建 result.txt 的前置条件是 source.txt 。如果当前目录中，source.txt 已经存在，那么make result.txt可以正常运行。

```makefile
source.txt:
    echo "this is the source" > source.txt
```

source.txt后面没有前置条件，就意味着它跟其他文件都无关，只要这个文件还不存在，每次调用 make，它都会生成。

生成多个文件的写法：

```makefile
source: file1 file2 file3
```

在这种写法中，`source` 只是一个伪目标。

#### 4.2.3 命令 (commands)

命令表示如何更新目标文件，是构建目标的具体指令，运行的结果通常是生成目标文件。

每行命令之前必须有一个 tab 键，亦可以用 `.RECIPEPREFIX` 声明。

```makefile
.RECIPEPREFIX = >
all:
> echo Hello, world
```

每个命令都是在一个单独的 shell 中执行的，且这些 shell 之间 **没有** 继承关系。

### 4.3 Makefile 语法

1. 注释 #
2. 回声(echoing) make会打印每条命令，然后再执行，这就叫做回声（echoing），在命令的前面加上@，就可以关闭回声。
3. 通配符 用于指定一组符合条件的文件名。
4. 模式匹配 类似正则的匹配方式。
5. 变量和赋值符 自定义变量调用使用 $() 调用 shell 变量使用 `$$HOME` 的形式。赋值符有 `= := ?= +=` 四种
    - = 赋值，类似于引用，即 c=a+b 若 a 的值之后发生了变化，c的值也会变化
    - := 和 = 正好相反
    - ?= 如果没有被赋值过就赋予等号后面的值
    - += 追加等号后面的值
6. 内置变量 \$(CC) 指向当前使用的编译器，\$(MAKE) 指向当前使用的Make工具，还有[很多](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)。
7. 自动变量
    - \$@ 指代当前目标，就是 Make 命令当前构建的那个目标。
    - \$< 指代第一个前置条件。比如，规则为 t: p1 p2，那么 \$< 就指代p1。
    - \$? 指代比目标更新的所有前置条件，比如，规则为 t: p1 p2，其中 p2 的时间戳比 t 新，\$? 就指代p2。
    - \$^ 指代所有的前置条件。
    - \$* 指代匹配符 % 匹配的部分， 比如 % 匹配 f1.txt 中的f1 ，\$* 就表示 f1。
    - \$(@D) 和 \$(@F) 分别指向 \$@ 的目录名和文件名。比如，\$@ 是 src/input.c，那么 \$(@D) 的值为 src ，$(@F) 的值为 input.c。
    - \$(<D) 和 \$(<F) 分别指向 \$< 的目录名和文件名。
8. 判断和循环 使用 Bash 语法
9. 函数
    - wildcard 通配符
        使用 `src = $(wildcard *.cpp)` 来获取工作目录下所有的 `.cpp` 文件列表
    - notdir 去除路径信息
        使用 `dir = $(notdir $(src))` 去除 src 中的路径信息
    - patsubst 匹配替换
        `obj=$(patsubst %.cpp,%.o,$(dir))` 或者 `obj=$(dir:%.c=%.o)` 把 dir 中后缀是 .cpp 的替换为 .o

## 5. g++

### 5.1 g++ 的基本用法：

编译器的默认动作：

1. 预处理 .i 文件

    ```shell
    g++ -E test.cpp -o test.i
    ```

2. 预处理后的文件转为汇编语言 .s 文件

    ```shell
    g++ -S test.i -o test.s
    ```

3. 将汇编语言变为目标代码 .o 文件

    ```shell
    g++ -c -test.s -o test.o
    ```

4. 链接生成可执行程序 bin 文件，-o 为将产生的可执行文件用指定的文件名

    ```shell
    g++ test.o -o test
    ```

5. -l 用于指定程序要链接的库，-l 参数紧接着就是库名字，例如 `lib.OpenNI.so` 的库名字是 OpenNI

    ```shell
    g++ test.cpp -lOpenNI
    ```

    存在于 `/lib` `/local/lib` `usr/local/lib` 的库文件可以用 -l 参数进行链接。

    -L 用于指定库文件所在的目录，例如 `-L/usr/X11R6/lib -lX11` 。还有一些高级用法暂时不懂...

6. -I 用于指定头文件所在的目录，可以加相对路径 `-I../`。 -include 用于指定头文件(因为代码里会 `#include xxx`，所以一般不用)。

7. -Wall、-w、-v

    ```shell
    -Wall   打印出gcc提供的警告信息

    -w      关闭所有警告信息

    -v      列出所有编译步骤
    ```

8. -g  编译的时候会产生调试信息

### 5.2 示例

如果多于一个的源码文件在 g++ 命令中指定，它们都将被编译并被链接成一个单一的可执行文件。例如:

1. 头文件，定义了具有一个函数的类

    ```c++
    /*speak.h*/
    #include <iostream>
    class Speak
    {
        public:
            void sayHello(const char *);
    };
    ```

2. 说明了函数的内容

    ```c++
    /* speak.cpp */
    #include "speak.h"
    void Speak::sayHello(const char *str)
    {
        std::cout << "Hello " << str << "\n";
    }
    ```

3. 使用 Speak 类

    ```c++
    /* hellospeak.cpp */
    #include "speak.h"
    int main(int argc,char *argv[])
    {
        Speak speak;
        speak.sayHello("world");
        return(0);
    }
    ```

4. 使用 `g++` 进行编译：

    ```shell
    g++ hellospeak.cpp speak.cpp -o hellospeak
    ```

## 6. 写一个通用的 Makefile 文件

```makefile
.PHONY all clean

CXX = g++
CXXFLAGS = -Wall -g
INCLUDES = -Iinclude
LIBS = -lm
TARGET = ../bin/demo

SRCDIR := src
TESTDIR := test
SRCOBJS := $(patsubst %.cpp,%.o,$(wildcard $(SRCDIR)/*.cpp))
TESTOBJS := $(patsubst %.cpp, %.o, $(wildcard $(TESTDIR)/*.cpp))
OBJS := $(SRCOBJS) $(TESTOBJS)

all: $(TARGET)

$(TARGET):$(OBJS)
    $(CXX) $(CXXFLAGS) $(INCLUDES) $(LIBS) $^ -o $@

%.o:%.cpp
    $(CXX) $(CXXFLAGS) $(INCLUDES) $< -c -o $@

clean:
    -@rm $(TARGET) $(OBJS)
```