# ysyx-ics 笔记 (乱序)

## CPP - C Preprocessor

记录CPP几个非常强大的功能，更多请看[GNU CPP 文档](https://gcc.gnu.org/onlinedocs/cpp/)：

1. [Stringizing](https://gcc.gnu.org/onlinedocs/cpp/Stringizing.html)

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

2. [Concatenation](https://gcc.gnu.org/onlinedocs/cpp/Concatenation.html)

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

3. [Variadic Macros](https://gcc.gnu.org/onlinedocs/cpp/Variadic-Macros.html)

一个宏函数可以使用`...` 和 `__VA_ARGS__` 接受任意多的参数，下面是几个例子

```c
#define eprintf(...) fprintf (stderr, __VA_ARGS__)
#define eprintf(args...) fprintf (stderr, args)
#define eprintf(format, ...) fprintf (stderr, format, __VA_ARGS__)
```
