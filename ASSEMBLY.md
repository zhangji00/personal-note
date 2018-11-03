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
3. 
  [ios之CPU寄存器0](http://www.cocoachina.com/cms/wap.php?action=article&id=22140)
  <br>[ios值CPU寄存器1](https://blog.cnbluebox.com/blog/2017/07/24/arm64-start/)

在arm64中WZR和XZR是0寄存器，它们的值一直是0，用W/XZR代替0应该是编码优化问题

在arm elf中.rel.plt段Offset域存放的是需要重定位的函数在.got表中的偏移(call fun -> .plt -> .got)，也就是说.rel.plt存放的是需要重定位符号的相关信息
[android PLT hook的很好文章(Xhook)](https://github.com/iqiyi/xHook/blob/master/docs/overview/android_plt_hook_overview.zh-CN.md)
、[关于延迟重定位](https://blog.csdn.net/qq_32400847/article/details/71001693)([原文](https://eli.thegreenplace.net/2011/11/03/position-independent-code-pic-in-shared-libraries/))

.rel.dyn记录了加载时需要重定位的变量，.rel.plt记录的是需要重定位的函数。

```
0002c000 <.got>:
   2c000:	0002bf10 	andeq	fp, r2, r0, lsl pc
	...
   2c00c:	00010cb8 			; <UNDEFINED> instruction: 0x00010cb8

00010ccc <calloc@plt>:
   10ccc:	e28fc600 	add	ip, pc, #0, 12    ; pc = 0x10ccc + 8(3级流水线), ip = pc + (0 << 12) = 0x10cd4
   10cd0:	e28cca1b 	add	ip, ip, #110592	; 0x1b000  ip = ip + 0x1b000 = 0x2bcd4
   10cd4:	e5bcf338 	ldr	pc, [ip, #824]!	; 0x338 ip + 0x338 == 0x2c00c落入.got表中
```
```
Relocation section '.rel.plt' at offset 0xb04 contains 53 entries:
 Offset     Info    Type            Sym.Value  Sym. Name
0002c020  00001116 R_ARM_JUMP_SLOT   00000000   printf@GLIBC_2.4
.
.
Symbol table '.dynsym' contains 68 entries:
   Num:    Value  Size Type    Bind   Vis      Ndx Name
    ..
    17: 00000000     0 FUNC    GLOBAL DEFAULT  UND printf@GLIBC_2.4 (2)
OffSet 就是该需重定位符号在GOT表中的位置，Info(低2字节有效)中高字节是该符号在.dynsym符号表中的索引低字节是type类型宏定义在elf.h中
0x11==17 
0x16==22
#define R_ARM_JUMP_SLOT         22      /* Create PLT entry */
```
