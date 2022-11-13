结构
/home/wwwroot/demo/public/index.php
/index.php/index/index?p=1&ps=2

SCRIPT_FILENAME: /home/wwwroot/demo/public/index.php
PHP_SELF: /index.php/index/index
SCRIPT_NAME: /index.php
PATHI_INFO: /index/index
QUERY_STRING: p=1&ps=2
URI: /index.php/index/index?p=1&ps=2
fastcgi_split_path_info 指令
该指令会重写 nginx 的 $fastcgi_script_name / $fastcgi_path_info 将请求路径赋值到 $fastcgi_path_info，也就是我们想要的 path_info。

index.php/news/1
$fastcgi_script_name: index.php
$fastcgi_path_info: news/1
## 一、通用配置
以下配置文件同 nginx.conf 同级

- pathinfo.conf
解析并设定 PATH_INFO
```
fastcgi_split_path_info ^(.+?\.php)(/.*)$;
set $path_info $fastcgi_path_info;
fastcgi_param PATH_INFO $path_info;
fastcgi_param SCRIPT_NAME $fastcgi_script_name;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
```
fastcgi.conf
环境变量，open_basedir 是个需要注意的环境变量，会限制 php 载入文件的路径。很多应用入口文件处于 siteApp/public/index.php 下的框架可能会受到限制。
```
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;

fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REQUEST_SCHEME     $scheme;
fastcgi_param  HTTPS              $https if_not_empty;

fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;

# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param  REDIRECT_STATUS    200;
fastcgi_param PHP_ADMIN_VALUE "open_basedir=$document_root/:/tmp/:/proc/";
```
- enable-php.conf
只启用 php 不开启 pathinfo
```
location ~ [^/]\.php(/|$)
{
    # try_files $uri =404;
    # fastcgi_pass  unix:/tmp/php-cgi.sock;
    fastcgi_pass  127.0.0.0:9000;
    fastcgi_index index.php;
    include fastcgi.conf;
}
```
enable-php-pathinfo.conf
启用 php 并开启 pathinfo
```
location ~ [^/]\.php(/|$)
{
    # try_files $uri =404; pathinfo 下绝对是 404
    # fastcgi_pass  unix:/tmp/php-cgi.sock;
    fastcgi_pass  127.0.0.0:9000;
    fastcgi_index index.php;
    include pathinfo.conf;
    include fastcgi.conf;
}
```
## 二、站点配置示例
通配 / 根请求，使用 try_files 指令来进行 static / dynamic request 分离和 index.php 的隐藏。

如果你不想让别人知道你使用的服务端语言是什么，很简单，把 index.php 改为 youwantkonwlanginmyserverhahahnoway.php 后将 try_files 指令更新为：
```
location / {
    # dispatch static/dynamic request and hidde index.php
    try_files $uri $uri/ /youwantkonwlanginmyserverhahahnoway.php?$query_string =404;
}
```
- 站点配置
vhosts/site.conf
```
server {
    listen 80 default_server reuseport;
    #listen [::]:80 default_server ipv6only=on;
    server_name _;
    index index.html index.htm index.php;
    root  /home/wwwroot/default;
    
    location / {
        # thinkphp 兼容模式
        # last 使用重写后的 url 再一次进入 location 解析
        # break 直接返回重写后的 url 对应的资源
        #if (!-e $request_filename){
        #    rewrite  ^(.*)$  /index.php?s=$1  last;
        #}
        
        # dispatch static/dynamic request and hidde index.php
        # 隐藏 index.php /
        try_files $uri $uri/ /index.php$is_args$query_string =404;
        # 隐藏 index.php 并开启 pathinfo
        try_files $uri $uri/ /index.php$uri?$query_string;
    }
    
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        expires 30d;
    }

    location ~ .*\.(js|css)?$ {
        expires 12h;
    }

    location ~ /.well-known {
        allow all;
    }

    location ~ /\. {
        deny all;
    }
    
    # 防止上传 web-shell your documentRoot's directory
    location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }
    
    # 选择要启用的模式
    # include enable-php.conf
    include enable-php-pathinfo.conf
    
    access_log  /home/wwwlogs/access.log;
    error_log   /home/wwwlogs/error.log;
}
```
## 三、php 多版本服务
如果你能本地有多个站点，需要使用不同的 php 版本，则可以为不同版本的 php-fpm 服务创建不同的监听端口，再分别创建相应的 fpm 服务转发配置文件即可。

enable-php74-pathinfo.conf
```
location ~ [^/]\.php(/|$)
{
    # try_files $uri =404;
    fastcgi_pass  127.0.0.0:9074;
    fastcgi_index index.php;
    include pathinfo.conf;
    include fastcgi.conf;
}
```
enable-php56-pathinfo.conf
```
location ~ [^/]\.php(/|$)
{
    # try_files $uri =404;
    fastcgi_pass  127.0.0.0:9056;
    fastcgi_index index.php;
    include pathinfo.conf;
    include fastcgi.conf;
}
```
enable-php53-pathinfo.conf
```
location ~ [^/]\.php(/|$)
{
    # try_files $uri =404;
    fastcgi_pass  127.0.0.0:9053;
    fastcgi_index index.php;
    include pathinfo.conf;
    include fastcgi.conf;
}
```
