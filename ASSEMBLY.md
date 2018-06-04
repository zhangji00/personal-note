1. BL与BLX:
   <br>BL等效goto, 有去无回, 函数内跳转到指定代码块执行
   <br>BLX等效call, 有去有回, L就是保存返回地址到LR寄存器。跳转到子函数执行，结束返回
   <br>hardfault 有时就是LR寄存器的值不对让系统跑飞了

