# ysyx-ics 笔记 (乱序)

## CPP - C Preprocessor

记录CPP几个非常强大的功能，更多请看[GNU CPP 文档](https://gcc.gnu.org/onlinedocs/cpp/)：

### 1. [Stringizing](https://gcc.gnu.org/onlinedocs/cpp/Stringizing.html)

```c
#define xstr(s) str(s)
#define str(s) #s
#define foo 4

/**
 * str (foo)
 *      → "foo"
 * xstr (foo)
 *      → xstr (4)
 *      → str (4)
 *      → "4"
 */
```

### 2. [Concatenation](https://gcc.gnu.org/onlinedocs/cpp/Concatenation.html)

`##` 用来将两个 token 进行拼接 (CPP 会把源文件识别成一个一个的 token，这个过程叫 tokenization)

下面的C代码可以用 `##` 和 `#` 进行简化

```c
struct command
{
  char *name;
  void (*function) (void);
};

struct command commands[] =
{
  { "quit", quit_command },
  { "help", help_command },
  // ...
};
```

简化后的样子

```c
#define COMMAND(NAME)  { #NAME, NAME ## _command }

struct command commands[] =
{
  COMMAND (quit),
  COMMAND (help),
  // ...
};
```

### 3. [Variadic Macros](https://gcc.gnu.org/onlinedocs/cpp/Variadic-Macros.html)

一个宏函数可以使用`...` 和 `__VA_ARGS__` 接受任意多的参数，下面是几个例子

```c
#define eprintf(...) fprintf (stderr, __VA_ARGS__)
#define eprintf(args...) fprintf (stderr, args)
#define eprintf(format, ...) fprintf (stderr, format, __VA_ARGS__)
```

## getopt

使用 C/C++ 获取命令行参数常见的 (1) argparse 库，(2) boost program_options 库，(3) getopt 标准库函数，
这里记录下 getopt 的使用方法，详见[man](https://www.man7.org/linux/man-pages/man3/getopt.3.html)。

首先，`getopt` 一般会被循环调用，每次调用获取一个命令行参数，正常返回选项字符的ASCII值，在没有剩余的命令行参数后返回 -1。

`getopt` 的第三个参数 `optstring` 形如 `-bhl:d:p:`，在这个字符串中 `-`，`:`，`;` 的字符是合法选项字符，
如果这个合法的字符后面跟着`:`，则表示其需要一个参数跟着，跟着的参数可以通过全局变量 `optarg` 来获取。

> [!NOTE]
> 如果 `optstring` 的第一个字符是 `-`，那么扫描到非选项元素会返回 1
>

## GDB (LLDB)

GDB 官方的文档在 [这里](https://sourceware.org/gdb/current/onlinedocs/gdb)。

> [!NOTE]
> [LLDB](https://lldb.llvm.org/index.html) 是类似的工具，但是一般在 Mac M系列机上使用。
>

* [单步执行进入你感兴趣的函数](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Continuing-and-Stepping.html#Continuing-and-Stepping:~:text=the%20problem%20happen.-,step,-Continue%20running%20your)
  * s/step
* [单步执行跳过你不感兴趣的函数(例如库函数)](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Continuing-and-Stepping.html#Continuing-and-Stepping:~:text=but%20function%20calls%20that%20appear%20within%20the%20line%20of%20code%20are%20executed%20without%20stopping)
  * n/next
* [运行到函数末尾](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Continuing-and-Stepping.html#Continuing-and-Stepping:~:text=line%20debug%20information.-,finish,-Continue%20running%20until)
  * finish
* [打印变量或寄存器的值](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Data.html)
  * p/print
* [扫描内存](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Memory.html)
  * x/examine
* 查看[调用栈](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Frames.html)
  * f/frame
  * [bt/backtrace](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Backtrace.html)
* [设置断点](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Set-Breaks.html)
  * b/break
* [设置监视点](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Set-Watchpoints.html)
  * watch

> [!TIP]
> 如果你想[保存你的断点信息](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Save-Breakpoints.html)，
> 并在下一次运行 gdb 时能够重新载入，可以使用 `save breakpoints [filename]`，然后下次运行
> 时使用 `source [filename]` 重新载入断点
>

## Readline Library

如何使得我们可以在命令行中使用上下方向键查看历史指令呢？`readline` 可以帮我们做到，详情请看[GNU Readline Library官方文档](https://tiswww.case.edu/php/chet/readline/rltop.html)。
