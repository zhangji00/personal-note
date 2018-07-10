1. find / -name ip_forward | xargs -t -I {} bash -c 'echo 1 > "$1"' _ {} 用find找到ip_forward 并用xargs将管道的输出流变成下条指令的参数(有的指令并不接受输入流只接受参数); -t 是执行命令前显示命令， -I 是字符串替换。 bash -c 会将后一条字符串作为命令行解析，字符串之后的第一个参数将作为该字符指令的$0,第二个之后的将是该命令的参数

2. find / -name ip_forward -exec bash -c 'echo 1 > "$1"' _ {} \; 具有同样效果\; 是转义作为终止符

3. find / -name ip_forward | xargs -t -I {} cat {} 用来查看ip_forward值(linux 网络封包转发)

4. netstat -n | awk '/^tcp/ {++State[$NF]} END {for (i in State) print i, State[i]}'统计网络状态 awk ^tcp用于匹配开头包含tcp字符的文本行，$NF 指的是每行最后一个字段数组State[$NF]就是以最后一个字段为下标指向一个存储单元或者说变量此处指向该字段的统计值也就是++的意义所在， 统计值自增一。END不能缺少表示之后指令于处理结束时执行i是字段，State[i]即为统计值每行处理完成则State[$NF] 加1
```
  text='any haha\nany  hehe\nany  haha'
  echo -e $text | awk '/any/ {++State[$NF]} END {for (i in State) print i, State[i]}'
```
```
     haha 2
     hehe 1
```
awk处理第一行时haha是最后一个字段$NF == haha => ++State[haha]awk处理第二行时hehe是最后一个字段$NF == hehe => ++State[hehe]awk处理第三行时haha是最后一个字段$NF == haha => ++State[haha]结束时State[haha] == 2 State[hehe] == 1

5. observer 远程调试服务器应用elixir 应用跑在服务器上想监测程序状态？observer 无法直接运行在ssh登录的无界面环境中此时可以利用ssh建立通道让本地端口重定向到远程服务器服务器端:
```
     1. iex --name serv@127.0.0.1 --cookie any
        Erlang/OTP 20 [erts-9.0] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]
        Interactive Elixir (1.4.5) - press Ctrl+C to exit (type h() ENTER for help)
        iex(serv@127.0.0.1)1>
     
     2. epmd -names
        epmd: up and running on port 4369 with data:
        name serv at port 51097
```
本地端:
```
   1. ssh -L 4396:localhost:4396 -L 51097:localhost:51097 root@server
   2. iex --name client@127.0.0.1 --cookie any
```
在本地端iex中开启observer后即可在node中找到serv@127.0.0.1这个做法其实就是利用ssh的-L在本地绑定一个端口,使得所有本地访问此端口的数据全部重定向至服务器,从而架起了一个本地和服务器的数据通信桥梁其实epmd是Erlang Port Mapper Daemon, 在分布式erlang应用中充当类似dns的名称到端口的映射4369即是该服务监听的端口，此例中51097是我们开启的节点serv所对应的端口。通过ssh -L 我们就可以轻松实现非局域网内的节点访问监测相当实用, 当然也用Wobserver这样的专用项目可供使用[Wobserver](https://github.com/shinyscorpion/wobserver)

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

7. elf文件头的Entry point address字段值是text段的起始地址但不是main函数的起始地址，而是\_start函数的地址（由编译器添加的初始化函数）\_start -> \_\_libc\_start\_main -> main

8. 在32位linux操作系统中每个进程都有4GB的虚拟空间, 每个进程的虚拟空间中高地址都是内核映像，低地址是用户空间，所有需要用到的动态库都会被映射到mmap段（注意是每个进程结构一致，也就是说每个进程都要给自己的虚拟空间加载共享库。难道进程不会因此占用太多内存吗？还是说映射只是个地址然后跳转？这句话给了我答案--可执行程序并非真正读到物理内存中，而只是链接到进程的虚拟内存中（此时都是在进程的虚拟地址空间）因此可以说就是映射不过是完整的映射可访问的，地址转化就是操作系统的事了）。
