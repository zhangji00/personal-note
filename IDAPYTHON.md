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

create_strlit(ea, endea)可以用来创建字符串，有的时候.rodata段被ida当做函数转换成汇编，可以用del_items(ea)来是当前地址的条目为unknown(MakeUnkn)再用create_strlit(ea, BADADDR)来创建字符串。endea用BADADDR则字符串长度由内核计算一般是以0x00结尾(C-style）

将.rodata段的未定义或错误定义的数据转换为字符串脚本
```
from ida_bytes import ALOPT_IGNHEADS, ALOPT_IGNPRINT, ALOPT_IGNCLT, ALOPT_MAX4K
start = 0x26bdbf
end = SegEnd(start)
OPS_ALL = ALOPT_IGNHEADS | ALOPT_IGNPRINT | ALOPT_IGNCLT | ALOPT_MAX4K 
step = 4
while start < end:
    str_len = ida_bytes.get_max_strlit_length(start, STRTYPE_TERMCHR, OPS_ALL)
    print "proc ", start, " len ", str_len
    if str_len == 0:
        start += 1
        continue
    str_end = start + str_len
    str_start = start
    while str_start < str_end:
        del_items(str_start)
        str_start += 1
    create_strlit(start, start + str_len)
    start += str_len
```
在ida中要添加函数的交叉应用光标置于函数处选择view -> Open subviews -> Cross references 右键Add cross-reference，删除类似

ida反汇编中函数栈中的参数定义STR R0, [SP, #n + var_m]其中n是栈的深度(要考虑函数所有的堆栈操作)，m是变量和栈底的距离(猜测因为SP是变动的，所以以BP为基础具有唯一性)

ida中如果误删数据类型（比如误删vtable）可以使用ida的区间分析强制ida重新分析一个区间内的bin。具体操作可以选中区间edit -> Code 会有Perform analysis or force conversion of the selected bytes to instruction(s)?的提示这时选择Analyze就好

用红色标记所有寄存器函数调用(虚表函数,ex: BL R2)
```
import time
for fun in Functions():
    for i in FuncItems(fun):
        if ida_ua.can_decode(i):
            if print_insn_mnem(i).find("B") == 0:
                if get_operand_type(i, 0) == o_reg and print_operand(i, 0) != "LR":
                    print i
                    set_color(i, CIC_ITEM, 0x0000ff)
                    jumpto(i)
                    time.sleep(7)
```
当ida反汇编超慢时，很可能是因为反汇编未结束就在Functions Window中搜索函数名导致的, DA's Functions window, as well as some other windows, presents sorted data. This means that for every function added, it sorts the entire view.所以出现这种情况直接关闭这个窗口就行了
