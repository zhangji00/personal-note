1. find / -name ip_forward | xargs -t -I {} bash -c 'echo 1 > "$1"' _ {} 
   <br>用find找到ip_forward 并用xargs将管道的输出流变成下条指令的参数(有的指令并不接受输入流只接受参数); 
   <br>-t 是执行命令前显示命令， -I 是字符串替换。 bash -c 会将后一条字符串作为命令行
   <br>解析，字符串之后的第一个参数将作为该字符指令的$0,第二个之后的将是该命令的参数
2. find / -name ip_forward -exec bash -c 'echo 1 > "$1"' _ {} \; 具有同样效果\; 是转义作为终止符
3. find / -name ip_forward | xargs -t -I {} cat {} 用来查看ip_forward值(linux 网络封包转发)
4. netstat -n | awk '/^tcp/ {++State[$NF]} END {for (i in State) print i, State[i]}'
   <br>统计网络状态 awk ^tcp用于匹配开头包含tcp字符的文本行，$NF 指的是每行最后一个字段
   <br>数组State[$NF]就是以最后一个字段为下标指向一个存储单元或者说变量此处指向该字段的统计值
   <br>也就是++的意义所在， 统计值自增一。
   <br>END不能缺少表示之后指令于处理结束时执行
   <br>i是字段，State[i]即为统计值
   <br>每行处理完成则State[$NF] 加1
   ```
     text='any haha\nany  hehe\nany  haha'
     echo -e $text | awk '/any/ {++State[$NF]} END {for (i in State) print i, State[i]}'
   ```
   ```
     haha 2
     hehe 1
   ```
   <br>awk处理第一行时haha是最后一个字段$NF == haha => ++State[haha]
   <br>awk处理第二行时hehe是最后一个字段$NF == hehe => ++State[hehe]
   <br>awk处理第三行时haha是最后一个字段$NF == haha => ++State[haha]
   <br>结束时State[haha] == 2 State[hehe] == 1
