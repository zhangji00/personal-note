1. B与BL:
   <br>B等效goto, 有去无回, 函数内跳转到指定代码块执行
   <br>BL等效call, 有去有回, L就是保存返回地址到LR寄存器。跳转到子函数执行，结束返回
   <br>hardfault 有时就是LR寄存器的值不对让系统跑飞了

2.
```
.text:0 call $+5
.text:5 pop ebp
.text:6 lea ebp, [ebp-1h]
...
```
该代码是为了获得偏移5($是当前地址)处的指令地址，call指令第一步是push eip即把下一条指令(返回地址)压栈，第二部是跳转到目标地址执行代码。此例中偏移5处指令实际是将栈顶的值弹出到ebp而这恰好是call指令的返回地址(其实也就是pop ebp的地址)，而此时没有ret所以函数并没有返回而是继续执行，且此时栈中并没有变换所以对整个函数而言栈环境并未改变(不用mov eax, eip是因为没有直接操作eip的指令)
应用实例
```
.text:03191E7B                 call    $+5
.text:03191E80                 pop     ebx
.text:03191E81                 add     ebx, 260ADD4h
.text:03191E87                 mov     al, ds:(byte_58A5266 - 579CC54h)[ebx] ; 0x3191E80 + 0x260ADD4 = 0x579cc54
.text:03191E8D                 and     al, 1
```
以上代码ebx的值就是0x579CC54。先取出pop ebx指令的地址0x3191E80到ebx，再加上0x260ADD4，结果就是0x579CC54（跟ARM中取字符串的指令很像）。 饶了这么多其实就是取数据段byte_58A5266的值到al寄存器，并做相应处理
