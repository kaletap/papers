# How language parsers work?

https://tomassetti.me/parsing-in-python/

Parsers:
1. lexer: scans through the text and assigns types to various words (like Num, Reserved keyword, variable)
2. proper parser: produces Parse Tree (or Abstract Syntax Tree, AST)
![](img/abstract_syntax_tree.png)

What can we do with AST? Validate it and interpret/compile.

What is Grammar? "Formal description of a language that can be used to recognise its structure".

Types of Languages and Grammars.

Chomsky hierarchy of languages: regular languages, context-free languages

It is possible to write your own parser generator with Python. There exist tools for definig a grammer, which will produce AST.

What does it mean that Python is an interpreted language?

That it's executed 'one by one' and not compiled before like C. Code is transformed into bytecode, which is platform independent.
