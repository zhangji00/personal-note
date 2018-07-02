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
5. observer 远程调试服务器应用
   <br>elixir 应用跑在服务器上想监测程序状态？observer 无法直接运行在ssh登录的无界面环境中
   <br>此时可以利用ssh建立通道让本地端口重定向到远程服务器
   <br>服务器端:
   ```
        1. iex --name serv@127.0.0.1 --cookie any
           Erlang/OTP 20 [erts-9.0] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]
           Interactive Elixir (1.4.5) - press Ctrl+C to exit (type h() ENTER for help)
           iex(serv@127.0.0.1)1>
        
        2. epmd -names
           epmd: up and running on port 4369 with data:
           name serv at port 51097
   ```
   <br>本地端:
   ```
      1. ssh -L 4396:localhost:4396 -L 51097:localhost:51097 root@server
      2. iex --name client@127.0.0.1 --cookie any
   ```
   <br>在本地端iex中开启observer后即可在node中找到serv@127.0.0.1
   <br>这个做法其实就是利用ssh的-L在本地绑定一个端口,使得所有本地访问此端口的数据全部重定向
   <br>至服务器,从而架起了一个本地和服务器的数据通信桥梁
   <br>其实epmd是Erlang Port Mapper Daemon, 在分布式erlang应用中充当类似dns的名称到端口的映射
   <br>4369即是该服务监听的端口，此例中51097是我们开启的节点serv所对应的端口。通过ssh -L 我们
   <br>就可以轻松实现非局域网内的节点访问监测相当实用, 当然也用Wobserver这样的专用项目可供使用
   <br>[Wobserver](https://github.com/shinyscorpion/wobserver)
6. udptunnel 脚本启动
```
echo start servlot
SERVER=192.187.100.106
if [ "$1" = "" ]; 
then 
  echo you should specify a port otherwise will use defalut 9999;
  PORT=9999;
else
  PORT=$1
fi  
KILL_AND_RESTART="
 tunnel=\`screen -ls | grep tunnel | grep -v grep | cut -d . -f 1\`;
 for x in \$tunnel ; 
 do  
   echo kill screen \$x;
   screen -r \$x -X quit;
 done;
 echo listen at port $PORT;
 screen -dmS tunnel /root/udptunnel/udptunnel -p $PORT 9.9.9.9 $PORT;
"
#echo $KILL_AND_RESTART;
ssh root@$SERVER $KILL_AND_RESTART
echo start client
screen -r client -X quit
screen -r sshproxy -X quit
screen -dmS client `dirname $0`/udptunnel -c $PORT $SERVER $PORT 127.0.0.1 22
sleep 3; #-i /root/.ssh/SERVER2
screen -S sshproxy ssh -v  -D *:1080 -p $PORT root@127.0.0.1
```  
