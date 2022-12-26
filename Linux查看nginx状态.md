###Linux查看nginx状态

简介：Nginx是一个高性能的反向代理服务器，现在一般作为我们网站或其他Web服务的第一层代理，它负责监听80端口，用户通过浏览器发送的请求首先经过的就是Nginx服务。如果Nginx没有启动或异常结束就会影响Web服务的正常使用。

### 一、查看是否启动
场景例子：当发现网页无法正常访问时（开发环境、测试环境的网页），可以查看Nginx是否启动，方法有以下几种：

1-- ps查看进程，利用nginx关键字过滤

    ps -aux | grep nginx

2--直接查看进程pid

    ps -C nginx -o pid

    这种直接返回pid的方式比较适合跟其他程序结合使用，比如在shell/python脚本中执行这个命令拿到pid，让后根据pid来判断Nginx是否启动。

3--查看端口状态

    nginx默认服务端口是80，直接查看端口80是否被占用，被谁占用即可；

    lsof -i:80

    也可以通过查看80端口运行的程序来判断Nginx是否运行

    netstat -anp | grep :80

二、启动、停止、重启Nginx
启动nginx：nginx安装目录地址 -c nginx配置文件地址

停止nginx:

    1:ps -ef | grep nginx 查出进程id， kill -9 进程id 杀死进程

    2:pkill -9 nginx  强制暂停

重启nginx：

进入nginx可执行目录sbin下，输入命令./nginx -s reload 即可

验证配置文件的方法：

进入nginx安装目录sbin下，输入命令./nginx -t

