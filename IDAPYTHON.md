IDAPython由三个独立模块组成:
1. idc，它是封装IDA的IDC函数的兼容性模块。
2. idautils，这是IDA里的一个高级实用函数。
3. 第三个模块是idaapi，它允许访问更多低级数据，这些数据能够被类使用通过IDA。

## links
1. https://www.cnblogs.com/goabout2/p/5092519.html
2. https://blog.csdn.net/u011337769/article/details/77428914
3. https://programtalk.com/python-examples/idautils./
3. https://github.com/idapython/src/tree/master/examples
4. struct: https://blog.csdn.net/hgy413/article/details/7104304
5. https://github.com/onethawt/idaplugins-list

如下指令中call函数,IDA为了给重载函数生成唯一的名称，编译器使用符号重组来显示函数名称
```
call    __ZN6DeriveC1Ev ; Derive::Derive(void)
```
其真实的名字应该为注释中的字符串，为了显示真实函数名，使用Options▶Demangled Names 
1. Comments 注释显示原名
2. Names 直接显示原名
3. Don't demangle 不显示原名
注: demangle 符号重组

在ida中python api函数可以在[api文档](https://www.hex-rays.com/products/ida/support/idapython_docs/)中搜索，但是idapython都是基于ida的[sdk](https://www.hex-rays.com/products/ida/support/sdkdoc/index.html)封装
print_insn_mnem(ea)用来输出指定地址的指令(mnemonics助记符)，print_operand(ea, n)用来输出指定地址的操作数，输出都为字符串。

Functions()是当前对象的所有的反编译函数生成器可以用来遍历函数，转换为list类型后其实都是地址

FuncItems(start)列举出start开始地址的函数中所有的指令开始地址，可以用来遍历函数中的指令

set_name(ea, name)修改或删除指定地址的名字。可以用来修改函数名，当name是""时会删除名称，如果删除的是函数名，则会恢复ida的保留名比如sub_****

Segments()返回所有段的起始地址，可以通过get_segm_name(ea)获取段名
 	
GetInstructionList()可以获得当前处理器的所有指令，这个应该可以用来搜索特定的指令，比如说搜索移位指令

GetRegisterList()可以获得当前处理器的所有寄存器名


