#在centos下启动nginx出现Failed to start nginx.service:unit not found

错误的原因就是没有添加nginx服务，所以启动失败。

###解决方法：

1.    在/root/etc/init.d/目录下新建文件，文件名为nginx

　　或者用命令在根目录下执行:# vim /etc/init.d/nginx    (注意vim旁边有一个空格)

 

2.    插入以下代码 


```javascript

#!/bin/sh
# nginx - this script starts and stops the nginx daemin
#
# chkconfig:   - 85 15
 
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
 
# processname: nginx
# config:      /usr/local/nginx/conf/nginx.conf
# pidfile:     /usr/local/nginx/logs/nginx.pid
 
# Source function library.
 
. /etc/rc.d/init.d/functions
 
# Source networking configuration.
 
. /etc/sysconfig/network
 
# Check that networking is up.
 
[ "$NETWORKING" = "no" ] && exit 0
 
nginx="/usr/local/nginx/sbin/nginx"
 
prog=$(basename $nginx)
 
NGINX_CONF_FILE="/usr/local/nginx/conf/nginx.conf"
 
lockfile=/var/lock/subsys/nginx
 
start() {
 
    [ -x $nginx ] || exit 5
 
    [ -f $NGINX_CONF_FILE ] || exit 6
 
    echo -n $"Starting $prog: "
 
    daemon $nginx -c $NGINX_CONF_FILE
 
    retval=$?
 
    echo
 
    [ $retval -eq 0 ] && touch $lockfile
 
    return $retval
 
}
 
 
stop() {
 
    echo -n $"Stopping $prog: "
 
    killproc $prog -QUIT
 
    retval=$?
 
    echo
 
    [ $retval -eq 0 ] && rm -f $lockfile
 
    return $retval
 
}
 
 
 
restart() {
 
    configtest || return $?
 
    stop
 
    start
 
}
 
 
reload() {
 
    configtest || return $?
 
    echo -n $"Reloading $prog: "
 
    killproc $nginx -HUP
 
    RETVAL=$?
 
    echo
 
}
 
force_reload() {
 
    restart
 
}
 
 
configtest() {
 
  $nginx -t -c $NGINX_CONF_FILE
 
}
 
 
 
rh_status() {
 
    status $prog
 
}
 
 
rh_status_q() {
 
    rh_status >/dev/null 2>&1
 
}
 
case "$1" in
 
    start)
 
        rh_status_q && exit 0
        $1
        ;;
 
    stop)
 
 
        rh_status_q || exit 0
        $1
        ;;
 
    restart|configtest)
        $1
        ;;
 
    reload)
        rh_status_q || exit 7
        $1
        ;;
 
 
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
 
 
    condrestart|try-restart)
 
        rh_status_q || exit 0
            ;;
 
    *)
 
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```


####  用命令进入此目录

　　# cd /etc/init.d

 

4. 依此执行以下命令

# chmod 755 /etc/init.d/nginx

# chkconfig --add nginx   (注意add前面是两个短横线-)

5.service nginx status 报错



错误:Failed to start SYSV: Nginx is an HTTP(S) server, HTTP(S) reverse

这里其实没什么什么问题,只是很多时候我们都先用/usr/local/nginx/sbin/nginx来启动了nginx

只要找到这个进程kill掉以后,再执行/etc/rc.d/init.d/nginx start就一切正常了

命令:

        killall -9 nginx

        /etc/rc.d/init.d/nginx start

       然后就可已用service nginx restart|start|stop.....来启动nginx服务了!!!
