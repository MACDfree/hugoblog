---
title: CentOS注册服务脚本
date: 2016-08-26T12:44:52
categories: ["Linux"]
tags: ["bash"]
---

CentOS中服务脚本示例模板及注册方法
<!--more-->

公司测试系统使用wrapper+tomcat部署，在CentOS环境上通过执行wrapper启动系统，但由于是在终端中执行，当退出终端后wrapper也会退出，所以想到将wrapper注册成系统服务。

<!--more-->

首先需要编写启动服务的脚本：
``` bash
#!/bin/bash

#description: 这是一个系统服务脚本
#chkconfig: 2345 20 81 

EXEC_PATH=/usr/local/          #需要注册的可执行文件所在文件夹的路径
EXEC=hello.sh                  #需要注册的可执行文件的文件名
DAEMON=/usr/local/hello.sh     #需要注册的可执行文件的完整文件名
PID_FILE=/var/run/hello.sh.pid #指定运行时的进程号

. /etc/rc.d/init.d/functions   #调用通用方法

if [ ! -x $EXEC_PATH/$EXEC ]; then
    echo "ERROR: $DAEMON not found"
    exit 1
fi

stop()
{
    echo "Stoping $EXEC ..."
    ps aux | grep "$DAEMON" | kill -9 `awk '{print $2}'` >/dev/null 2>&1
    rm -f $PID_FILE
    usleep 100
    echo "Shutting down $EXEC: [  OK  ]"
}

start()
{
    echo "Starting $EXEC ..."
    $DAEMON > /dev/null &
    pidof $EXEC > $PID_FILE
    usleep 100
    echo "Starting $EXEC: [  OK  ]"
}

restart()
{
    stop
    start
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    restart)
        restart
        ;;
    status)
        status -p $PID_FILE $DAEMON
        ;;
    *)
        echo "Usage: service $EXEC {start|stop|restart|status}"
        exit 1
esac

exit $?
```
完成脚本编写后执行以下命令注册成服务

```
chmod 700 test.sh             #设置脚本可执行
cp test.sh /etc/rc.d/init.d/  #拷贝脚本
chkconfig --add test.sh       #注册服务
chkconfig --list              #显示注册的服务
chkconfig  --del test.sh      #删除服务
```
注意点：
启动脚本中 `#description:` 和 `#chkconfig: 2345 20 81` 是必须的，否则执行 `chkconfig --add test.sh` 时会提示错误。

补充：
`#chkconfig`  中 `2345 20 81` 分别表示：
 
- 2345表示服务的运行级别
    0：停机
    1：单用户模式
    2：多用户，没有 NFS
    3：完全多用户模式(标准的运行级)
    4：没有用到
    5：X11 多用户图形模式（xwindow)
    6：重新启动

- 20表示启动的优先级
    
- 81表示关闭的优先级
