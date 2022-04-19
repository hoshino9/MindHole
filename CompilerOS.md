# Compiler OS

将编译器实现为一个 “操作系统”。
编译输入则是用户的 **操作**，编译输出则是 **操作的结果**。
**操作** 通常是一份代码，它能够修改 _操作系统_ 的行为。

## Initial

编译器开始运行时所执行的东西，实现为最基础语法的脚本。
它将加载 Lexer，Parser，优化等。

## Lexer

语法分析器，实现为最基础语法的脚本。
默认的语法分析器能够分析最基础分语法脚本。

```
lex(string) : list token
- 输入一个字符串，返回一个 token 列表

lex(string, callback) : unit
- 输入一个字符串，每成功分析一个 token 则调用 callback

clone() : lexer
- 返回该语法分析器的拷贝
```

## Parser

词法分析器，实现为最基础语法的脚本。
默认的词法分析器能够分析默认的语法分析器的输出。

```
parse(list token) : ast
- 输入一个 token 列表，返回一个抽象语法树

parse(parser) : token -> unit
- 接受一个 parser，返回该 parser 可用于 lexer 的 callback

clone() : parser
- 返回该词法分析器的拷贝
```

## 其他部分

也许是某些优化

## OS

操作系统部分，实现了一些基础的函数

```
eval(string, compiler) : compiler.output
- 接受一个字符串和编译器，返回该编译器接受字符串的输出

set_lexer(lexer) : unit
- 设置全局 lexer，在这个语句执行成功后，所有的输入都会交由给新的 lexer 处理

get_lexer() : lexer
- ...

set_parser(parser) : unit
- 设置全局 parser，在这个语句执行成功后，所有的 lexer 输出都会交由给新的 parser 处理

get_parser() : parser
- ...

set_optimize(optmize) : unit
- 设置全局优化

get_optmize() : optmize
- ...
```

## UI

用户界面，默认是一个 repl。
