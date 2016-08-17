## GO 语言编写的 执行 js 脚本的程序

## js 脚本demo

```
var s = function (){
    var b=1;
    var c=2
    return b+c;
}

console.log(s())
```

由于在 `window` 下 没有很好的 `js ` 脚本解析器

所以在这里用 `otto` 编译个 `js` 脚本解析器

`window` 下 建议将 `gojs.exe` 加入环境变量

在 `cmd` 命令运行 要想获得 `js`  返回值  `console.log(s())` 即可


## php 通过 shell 环境执行命令

```
<?php
$output = shell_exec('gojs a.js');
echo "<pre>$output</pre>";
?>
```

当前 `php` 有 V8 引擎库 可以之间执行 js 脚本

## python 简单


```
import os
str = os.popen("gojs a.js").read()
a = str.split("\n")
for b in a:
   print b
```



## python 高级

```
# coding=utf-8

import shlex
import datetime
import subprocess
import time
 
def execute_command(cmdstring, cwd=None, timeout=None, shell=False):
    """执行一个SHELL命令
            封装了subprocess的Popen方法, 支持超时判断，支持读取stdout和stderr
           参数:
        cwd: 运行命令时更改路径，如果被设定，子进程会直接先更改当前路径到cwd
        timeout: 超时时间，秒，支持小数，精度0.1秒
        shell: 是否通过shell运行
    Returns: return_code
    Raises:  Exception: 执行超时
    """
    if shell:
        cmdstring_list = cmdstring
    else:
        cmdstring_list = shlex.split(cmdstring)
    if timeout:
        end_time = datetime.datetime.now() + datetime.timedelta(seconds=timeout)
    
    #没有指定标准输出和错误输出的管道，因此会打印到屏幕上；
    sub = subprocess.Popen(cmdstring_list, cwd=cwd, stdin=subprocess.PIPE,shell=shell,bufsize=4096)
    
    #subprocess.poll()方法：检查子进程是否结束了，如果结束了，设定并返回码，放在subprocess.returncode变量中 
    while sub.poll() is None:
        time.sleep(0.1)
        if timeout:
            if end_time <= datetime.datetime.now():
                raise Exception("Timeout：%s"%cmdstring)
            
    return str(sub.returncode)
 
if __name__=="__main__":
    print execute_command("gojs a.js")
```


