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

如下指令中call函数,IDA为了给重载函数生成唯一的名称，编译器使用符号重组来显示函数名称
```call    __ZN6DeriveC1Ev ; Derive::Derive(void)```
<br>其真实的名字应该为注释中的字符串
<br>为了显示真实函数名，使用Options▶Demangled Names 
1. Comments 注释显示原名
2. Names 直接显示原名
3. Don't demangle 不显示原名
<br>注: demangle 符号重组
