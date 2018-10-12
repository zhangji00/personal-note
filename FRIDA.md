[frida教程](http://www.ninoishere.com/frida-learn-by-example/)
<br>如果出现frida attach进程失败，一方面有可能是反调试，还有可能是frida-server因为使用&后台运行时间过长，这个时候应该先重启frida-server
```
.overload('[B') \\byteArray
.overloda('[C') \\charArray
```
Interceptor.attach(Module.findBaseAddress('libfoo.so').add(0x1234), ...
<br>Just keep in mind that the address needs to have its least significant bit set to 1 for Thumb functions
