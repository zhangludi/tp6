### cnetos安装redis

yum install redis

重新启动

```
sudo systemctl restart redis

sudo systemctl status redis

ss -an | grep 6379

```

1、下载redis扩展，php redis 的扩展一定要与你的PHP版本相对应，否则安装后是不能使用的。
版本不对时重启PHP fpm 时会报错：

```

Gracefully shutting down php-fpm . done
Starting php-fpm [27-Jan-2021 14:49:11] NOTICE: PHP message: PHP Warning:  PHP Startup: redis: Unable to initialize module
Module compiled with module API=20160303
PHP    compiled with module API=20200930
These options need to match
 in Unknown on line 0
 done


```

下载地址：http://pecl.php.net/package/redis
我这次下载的是：redis-5.3.2.tgz

```
cd /software/
wget https://pecl.php.net/get/redis-5.3.2.tgz
Bash

```

2、解压 并 进入解压后的目录

```
tar -zxvf redis-5.3.2.tgz
cd redis-5.3.2
Bash

```


3、配置、编译与安装

```

phpize
./configure --with-php-config=/usr/local/php8.0.1/bin/php-config
make && make install

````


4、在php.ini中添加redis的扩展

````
vim /usr/local/php8.0.1/etc/php.ini

```

添加以下一行

```
extension=redis.so

````

5、然后重启php fpm，不同的机器配置的重启方式会不一样，我这里使用脚本重启

````
service php-fpm restart
报错：



````


Centos7解决php: error while loading shared libraries: libonig.so.2报错
2020-08-16 分类：Linux / Web / 服务器 阅读(2896)
1、故障背景：
CentOS7 执行yum -y update 命令升级系统后，起初测试正常，在执行“php -V”测试报如下错误：

2、多方查找资料后，执行如下命令：

复制

```
ln -s /usr/lib64/libonig.so /usr/lib64/libonig.so.2

```
最后重启PHP后，故障解决。
