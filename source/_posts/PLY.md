---
title: PLY
date: 2017-01-23 13:49:45
tags:
- PLY
- 编译原理
---
*仅供个人学习使用，请以文档为准*

PLY是经典编译器构造工具lex和yacc的python实现，由`lex.py`和`yacc.py`两个模块组成
来看一个lex的例子：
<!-- more -->
```
import ply.lex as lex

reserved={
        'if':'IF',
        'then':'THEN',
        'else':'ELSE',
        'while':'WHILE',
        }
tokens=[
        'NUMBER',
        'PLUS',
        'MINUS',
        'TIMES',
        'DIVIDE',
        'LPAREN',
        'RPAREN',
        'ID',
        ]+list(reserved.values())

t_PLUS=r'\+'
t_MINUS=r'-'
t_TIMES=r'\*'
t_DIVIDE=r'/'
t_LPAREN=r'\('
t_RPAREN=r'\)'

def t_ID(t):
    r'[a-zA-Z][a-zA-Z0-9]*'
    t.type=reserved.get(t.value,'ID')
    return t

def t_NUMBER(t):
    r'\d+'
    t.value=int(t.value)
    return t

def t_newline(t):
    r'\n+'
    t.lexer.lineno+=len(t.value)

t_ignore=' \t'
t_ignore_COMMENT='\#.*'

def t_error(t):
    print("Illegal character '%s'" %t.value[0])
    t.lexer.skip(1)
lexer=lex.lex()

data='''
3 + 4 * 10
- 20 * 2
#I love python
'''

lexer.input(data)
for tok in lexer:
    print(tok)
```
首先，必须定义一个token list，包括所有lex可能产生的token name。

接着是转换规则，每个转换规则必须用`t_`开头，说直白点就是这样子写`t_PLUS=r'\+'`。
使用raw strings是因为这是在python中写正则表达式最简单的办法。
复杂的转换规则可以使用函数定义，同样使用`t_`开头，正则表达式写在`docstring`部分，举个栗子
```
def t_NUMBER(t):
    r'\d+'
    t.value=int(t.value)
    return t
```
这个函数只有一个LexToken类型的参数。LexToken有4个属性，`tok.type`,`tok.value`,`tok.lineno`,`tok.lexpos`。
如果修改了参数必须返回，否则lex丢弃这个token并读取下一个token。例如
```
def t_COMMENT(t):
    r'\#.*'
    pass
```
或者`t_ignore_COMMENT=r'\#.*'`，也可以丢弃token

对于保留字，我们可以提前填入符合表中，如果在符号表中的一个字段指明这些不是普通的标识符并指出他们代表的token

函数lex.lex()可以构建一个lexer，有两个函数可以用来控制lexer
- lexer.input(data)
输入新的data
- lexer.token()    
返回下一个token

lex.lex(debug=1)
> 开启debug模式
lex.lex(optimize=1)
如果python开启优化模式，lex必须传入这个参数这个才能正常工作，但是lex会禁用错误检查，所以请确保代码正确后再开启优化模式
lex.lex(module=moduleName)
>从模块中导入规则
一个lexer对象有一些在特定情况下很有用的内部属性：
- lexer.lexpos
一个数字，表示下一个token的位置
- lexer.lineno
行号，需要手动设置
- lexer.lexdata
lex.input()传入的数据
- lexer.lexmatch
re.match()函数返回的匹配当前token的Match对象，只有在函数内部可以使用

如果想要自己维护一些信息，如符号表，可以使用全局变量，或着把信息存入lexer对象，如
```
def t_NUMBER(t):
     r'\d+'     
     t.lexer.num_count += 1     # Note use of lexer attribute     
     t.value = int(t.value)         
     return t 
lexer = lex.lex() 
lexer.num_count = 0            # Set the initial count 
```