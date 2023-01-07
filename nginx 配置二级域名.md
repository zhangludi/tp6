### nginx 配置二级域名

```


#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
              root   /home/www;
              index  index.php index.html index.htm;    ##添加index.php并放在第一位

         location ~ \.php$ {  #取消这几行的注释
              root           html;
              fastcgi_pass   127.0.0.1:9000;
              fastcgi_index  index.php;
             fastcgi_param  SCRIPT_FILENAME  /home/www$fastcgi_script_name; #将路径改为nginx的网页路径
              include        fastcgi_params;
          }
		  }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
	
	include vhost/*;
}


```



#### 编辑vhost/***.conf

#### 主域名配置

```
  server
{
	listen 80;
	server_name zzz.cn;
	access_log logs/tp6.access.log;
	error_log logs/tp6.error.log;

	#index index.html index.htm index.php;
	#root ;
	
		location / {
              root   /home/www;
              index  index.php index.html index.htm;    ##添加index.php并放在第一位
				
				  proxy_set_header   X-Real-IP $remote_addr;
					proxy_set_header   Host      $http_host;
					proxy_pass         http://0.0.0.0:8000;
		
		
         location ~ \.php$ {  #取消这几行的注释
              root           html;
              fastcgi_pass   127.0.0.1:9000;
              fastcgi_index  index.php;
             fastcgi_param  SCRIPT_FILENAME  /home/www$fastcgi_script_name; #将路径改为nginx的网页路径
              include        fastcgi_params;
          }
		  }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
   
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    }
	
	

```

##### 二级域名配置

```

  server
{
	listen 80;
	server_name acb.zzz.cn;
	access_log logs/api.access.log;
	error_log logs/api.error.log;

	#index index.html index.htm index.php;
	#root ;
	
		location / {
              root   /home/www/api/public;
              index  index.php index.html index.htm;    ##添加index.php并放在第一位
				
				  proxy_set_header   X-Real-IP $remote_addr;
					proxy_set_header   Host      $http_host;
					proxy_pass         http://0.0.0.0:8001;
		
		
        location ~ \.php(.*)$ { # 正则匹配.php后的pathinfo部分
			root /home/www/api/public;
			fastcgi_pass   127.0.0.1:9000;
			fastcgi_index  index.php;
			fastcgi_param  SCRIPT_FILENAME  $DOCUMENT_ROOT$fastcgi_script_name;
			fastcgi_param PATH_INFO $1; # 把pathinfo部分赋给PATH_INFO变量
			include        fastcgi_params;
			}
		  }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
   
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    }
	
	

```


