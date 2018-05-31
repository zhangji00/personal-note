1. find / -name ip_forward | xargs -t -I {} bash -c 'echo 1 > "$1"' _ {} 
   <br>用find找到ip_forward 并用xargs将管道的输出流变成下条指令的参数(有的指令并不接受输入流只接受参数); 
   <br>-t 是执行命令前显示命令， -I 是字符串替换。 bash -c 会将后一条字符串作为命令行
   <br>解析，字符串之后的第一个参数将作为该字符指令的$0,第二个之后的将是该命令的参数
2. find / -name ip_forward -exec bash -c 'echo 1 > "$1"' _ {} \; 具有同样效果\; 是转义作为终止符
