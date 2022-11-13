[TOC]


#### 如何配置高性能的nginx

- 支持pathinfo

	nginx配置文件
	
```javascript
	
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

	   server
	{
		listen 80;
		server_name zl142.cn;
		#index index.html index.htm index.php;
		#root ;

			location / {
				  root   /home/www;
				  index  index.php index.html index.htm;    ##添加index.php并放在第一位
				  if (!e $request_filename) {
				  		rewirte ^/index.php(.*)$ /index.php?s=$1 last;
						rewirte ^(.*)$ /index.php?s=$1 last;
				  }  ##使nginx 支持pathinfo格式也能省略index.php

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

	}

```



