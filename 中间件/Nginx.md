## Nginx

#### 配置文件

- conf 详解

```bash
#定义Nginx运行的用户和用户组
user www www;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;

#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /var/log/nginx/error.log info;

#进程文件
pid /var/run/nginx.pid;

#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。
worker_rlimit_nofile 65535;

#工作模式与连接数上限
events
{
#参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型，如果跑在FreeBSD上面，就用kqueue模型。
use epoll;
#单个进程最大连接数（最大连接数=连接数*进程数）
worker_connections 65535;
}

#设定http服务器
http
{
include mime.types; #文件扩展名与文件类型映射表
default_type application/octet-stream; #默认文件类型
#charset utf-8; #默认编码
server_names_hash_bucket_size 128; #服务器名字的hash表大小
client_header_buffer_size 32k; #上传文件大小限制
large_client_header_buffers 4 64k; #设定请求缓
client_max_body_size 8m; #设定请求缓
sendfile on; #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
autoindex on; #开启目录列表访问，合适下载服务器，默认关闭。
tcp_nopush on;  #防止网络阻塞
tcp_nodelay on; #防止网络阻塞
keepalive_timeout 120; #长连接超时时间，单位是秒

#FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
fastcgi_connect_timeout 300;
fastcgi_send_timeout 300;
fastcgi_read_timeout 300;
fastcgi_buffer_size 64k;
fastcgi_buffers 4 64k;
fastcgi_busy_buffers_size 128k;
fastcgi_temp_file_write_size 128k;

#gzip模块设置
gzip on;        #开启gzip压缩输出
gzip_min_length 1k; #最小压缩文件大小
gzip_buffers 4 16k; #压缩缓冲区
gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）开始压缩的http协议版本(可以不设置,目前几乎全是1.1协议)
gzip_comp_level 2;   #推荐6压缩级别(级别越高,压的越小,越浪费CPU计算资源)
gzip_types text/plain application/x-javascript text/css application/xml;
#压缩类型，默认就已经包含text/html，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
gzip_vary on;   # 是否传输gzip压缩标志
#limit_zone crawler $binary_remote_addr 10m; #开启限制IP连接数的时候需要使用

# 反向代理，名字自定义
upstream shuiman.com {
#upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
server 192.168.80.121:80 weight=3;
server 192.168.80.122:80 weight=2;
server 192.168.80.123:80 weight=3;
}

#虚拟主机的配置
server
{
#监听端口
listen 80;
#域名可以有多个，用空格隔开
server_name www.shui.com man.com;
index index.html index.htm index.php;
root /data/www/shui;
location ~ .*\.(php|php5)?$
{
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
include fastcgi.conf;
}
#图片缓存时间设置
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
{
expires 10d;
}
#JS和CSS缓存时间设置
location ~ .*\.(js|css)?$
{
expires 1h;
}
#日志格式设定
log_format access '$remote_addr - $remote_user [$time_local] "$request" '
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" $http_x_forwarded_for';
#定义本虚拟主机的访问日志
access_log /var/log/nginx/ha97access.log access;

#对 "/" 启用反向代理
location / {
proxy_pass http://shuiman;
proxy_redirect off;
proxy_set_header X-Real-IP $remote_addr;
#后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#以下是一些反向代理的配置，可选。
proxy_set_header Host $host;
client_max_body_size 10m;     #允许客户端请求的最大单文件字节数
client_body_buffer_size 128k; #缓冲区代理缓冲用户端请求的最大字节数，
proxy_connect_timeout 90;     #nginx跟后端服务器连接超时时间(代理连接超时)
proxy_send_timeout 90;        #后端服务器数据回传时间(代理发送超时)
proxy_read_timeout 90;        #连接成功后，后端服务器响应时间(代理接收超时)
proxy_buffer_size 4k;         #设置代理服务器（nginx）保存用户头信息的缓冲区大小
proxy_buffers 4 32k;          #proxy_buffers缓冲区，网页平均在32k以下的设置
proxy_busy_buffers_size 64k;    #高负荷下缓冲大小（proxy_buffers*2）
proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传
}

#设定查看Nginx状态的地址
location /NginxStatus {
stub_status on;
access_log on;
auth_basic "NginxStatus";
auth_basic_user_file conf/htpasswd;
#htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
}

#本地动静分离反向代理配置
#所有jsp的页面均交由tomcat或resin处理
location ~ .(jsp|jspx|do)?$ {
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_pass http://127.0.0.1:8080;
}
#所有静态文件由nginx直接读取不经过tomcat或resin
location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$
{ expires 15d; }
location ~ .*.(js|css)?$
{ expires 1h; }
}
}
```



#### 常用配置案例

- 例子1

```bash
user  nginx nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        /var/run/nginx/nginx.pid;

events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #隐藏Nginx版本信息，禁止网站目录浏览
    server_tokens off;
    autoindex off;
    #当FastCGI后端服务器处理请求给出http响应码为4xx和5xx时，就转发给nginx
    fastcgi_intercept_errors on;

    #关于fastcgi的配置
    fastcgi_connect_timeout 300;    
    fastcgi_send_timeout 300;    
    fastcgi_read_timeout 300;    
    fastcgi_buffer_size 64k;    
    fastcgi_buffers 4 64k;    
    fastcgi_busy_buffers_size 128k;    
    fastcgi_temp_file_write_size 128k;

    #支持gzip压缩
    gzip on;
    gzip_min_length 1k;
    gzip_buffers 16 64k;
    gzip_http_version 1.1;
    gzip_comp_level 6;
    gzip_types text/plain application/x-javascript text/css application/javascript text/javascript image/jpeg image/gif image/png application/xml application/json;
    gzip_vary on;
    gzip_disable "MSIE [1-6].(?!.*SV1)";

    #
    # 重定向所有带www请求到非www的请求
    #
    server {
        listen               *:80;
        listen               *:443 ssl spdy;
        server_name www.typecodes.com;
        # ssl证书配置见文章 https://typecodes.com/web/lnmppositivessl.html
        ssl_certificate /etc/nginx/ssl/typecodes.crt;
        # ssl密钥文件见文章 https://typecodes.com/web/lnmppositivessl.html
        ssl_certificate_key /etc/nginx/ssl/typecodes.key;
        # 不产生日志
        access_log off;

        # 访问favicon.ico和robots.txt不跳转（把这两个文件存放在上级目录html中）
        location ~* ^/(favicon.ico|robots.txt)$ {
            root html;
            expires max;
            log_not_found off;
            break;
        }

        location / {
            return 301 https://typecodes.com$request_uri;
        }
    }

    #
    # 将所有http请求重定向到https
    #
    server {
        listen               *:80;
        server_name          shui.com;
        # 不产生日志
        access_log off;

        # 访问favicon.ico和robots.txt不跳转（把这两个文件存放在上级目录html中）
        location ~* ^/(favicon.ico|robots.txt)$ {
            root html;
            expires max;
            log_not_found off;
            break;
        }

        location / {
            return 301 https://shui.com$request_uri;
        }
    }

    #
    # HTTPS server
    #
    server {
        listen               *:443 ssl spdy;
        server_name typecodes.com;

        # ssl证书配置见文章 https://typecodes.com/web/lnmppositivessl.html
        ssl_certificate /etc/nginx/ssl/typecodes.crt;
        # ssl密钥文件见文章 https://typecodes.com/web/lnmppositivessl.html
        ssl_certificate_key /etc/nginx/ssl/typecodes.key;
        ssl_session_cache shared:SSL:20m;
        ssl_session_timeout 10m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA:!CAMELLIA;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #enables TLSv1, but not SSLv2, SSLv3 which is weak and should no longer be used.
        ssl_prefer_server_ciphers on;
        # 开启spdy功能
        add_header Alternate-Protocol 443:npn-spdy/3.1;
        # 严格的https访问
        add_header Strict-Transport-Security "max-age=31536000; includeSubdomains;";

        #设置网站根目录
        root   /usr/share/nginx/html/typecodes;
        index  index.php index.html;

        charset utf-8;

        #access_log  /var/log/nginx/log/host.access.log  main;

        #设置css/javascript/图片等静态资源的缓存时间
        location ~ .*\.(css|js|ico|png|gif|jpg|json|mp3|mp4|flv|swf)(.*) {
            expires 60d;
        }

        # include /etc/nginx/default.d/*.conf;
        # 设置typecho博客的config文章不被访问，保证安全
        location = /config.inc.php{
            deny  all;
        }

        # keep the uploads directory safe by excluding php, php5, html file accessing. Applying to wordpress and typecho.
        # location ~ .*/uploads/.*\.(php|php5|html)$ {
        #   deny  all;
        # }

        # 设置wordpress和typecho博客中，插件目录无法直接访问php或者html文件
        location ~ .*/plugins/.*\.(php|php5|html)$ {
            deny  all;
        }

        #Rewrite的伪静态(针对wordpress/typecho)，url地址去掉index.php
        location / {
            if (-f $request_filename/index.html){
                rewrite (.*) $1/index.html break;
            }
            if (-f $request_filename/index.php){
                rewrite (.*) $1/index.php;
            }
        if (!-f $request_filename){
                rewrite (.*) /index.php;
            }
        }

        #访问favicon.ico时不产生日志
        location = /favicon.ico {
            access_log off;
        }

        #设置40系列错误的应答文件为40x.html
        error_page  400 401 402 403 404  /40x.html;
        location = /40x.html {
                root   html;
                index  index.html index.htm;
        }

        #设置50系列错误的应答文件为50x.html
        #
        error_page   500 501 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
            index  index.html index.htm;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # 设置Nginx和php通信机制为tcp的socket模式，而不是直接监听9000端口
        location  ~ .*\.php(\/.*)*$ {
             fastcgi_split_path_info ^(.+\.php)(/.+)$;
             #fastcgi_pass   127.0.0.1:9000;
             # the better form of fastcgi_pass than before
             fastcgi_pass   unix:/var/run/php-fpm/php-fpm.sock;
             fastcgi_index  index.php;
             fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
             include        fastcgi_params;
        }

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
}
```

- 例子2

```bash
resolver 10.0.0.1;

# 负载均衡
upstream dynamic {    
    zone upstream_dynamic 64k;

    server backend1.example.com      weight=5;
    server backend2.example.com:8080 fail_timeout=5s slow_start=30s;
    server 192.0.2.1                 max_fails=3;
    server backend3.example.com      resolve;
    server backend4.example.com      service=http resolve;

    server backup1.example.com:8080  backup;
    server backup2.example.com:8080  backup;
}

# http服务
server {
    listen 80;
    server_name example.com www.example.com;
    location / {
        rewrite https://$host; # 重定向到https
    }
}

# https 服务
server {
    listen 443 ssl; # 监听端口
    server_name example.com www.example.com; # 匹配域名

    # ssl证书
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
    ssl_certificate     /usr/local/nginx/conf/cert.pem;
    ssl_certificate_key /usr/local/nginx/conf/cert.key;
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;

    # 静态服务
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }
    # 反向代理
    location /api {
        proxy_pass http://dynamic;
        health_check;
    }
}
```

---



#### 重定向 301

```bash
server {
    listen      80;
    server_name shui.cn;
    
    # 如果使用 shui.cn 直接访问，加前缀跳转
    if ($host = 'shui.cn'){
        return 301 https://www.shui.cn$request_uri;
    }
}
```



#### 全局 HTTPS

```bash
server {
    listen      80;
    server_name www.shui.cn;

    # 单域名重定向
    if ($host = 'www.shui.cn'){
        return 301 https://www.shui.cn$request_uri;
    }
    # 全局非 https 协议时重定向
    if ($scheme != 'https') {
        return 301 https://$host$request_uri;
    }

    # 或者全部重定向
    return 301 https://$host$request_uri;

    # 以上配置选择自己需要的即可，不用全部加
}
```



#### SSL 配置

```bash
listen      443 ssl http2 default_server; #http2支持，nginx1.9+支持
server_name www.shui.cn;

# nginx/1.15.0 以上使用 listen 443 ssl 代替 listen 443 和 ssl on
# ssl on;
# 证书路径
ssl_certificate             /etc/letsencrypt/live/shui.cn/fullchain.pem;
# 私钥路径
ssl_certificate_key         /etc/letsencrypt/live/shui.cn/privkey.pem;
ssl_session_cache           shared:SSL:10m;
ssl_session_timeout         30m;
# 浏览器已支持TLSv1.3，建议加上
# TLSv1和TLSv1.1即将废弃，如果不需要支持IE和XP建议去掉
# IE8-10/Win7需要TLSv1.0；IE8以下需要SSL3和单证书
ssl_protocols               TLSv1.1 TLSv1.2 TLSv1.3;
# on由服务器决定加密算法，off由浏览器决定
# 推荐使用on，更安全，对服务器性能有少量影响
ssl_prefer_server_ciphers   on;
# 使用此套接字加密，推荐配置
ssl_ciphers                 ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4:!DH:!DHE;

# HSTS(ngx_http_headers_module is required) (180d)
# 严格传输安全：即在时间过期之前，每次访问HTTP站点时，由客户端直接跳转到HTTPS站点
# 设置后，该网站的HTTP站点无法打开，只能等待过期或禁用配置后清空浏览器缓存
# 启用后注意保持证书不过期，证书过期后网站可能无法访问
add_header Strict-Transport-Security "max-age=15552000" always;

# 开启 OCSP Stapling，作用：由服务器在线查询证书吊销情况
# 默认是由浏览器在线查询，由服务器查询效率更高
ssl_stapling                on;

# OCSP Stapling 验证开启
ssl_stapling_verify         on;

# OCSP Stapling 的证书位置（完整的证书链，可选）
# ssl_trusted_certificate     /etc/letsencrypt/live/xinac.cn/chain.pem;

# 配置用于查询 OCSP 服务器的DNS（可选）
# resolver 223.5.5.5 223.6.6.6 valid=300s;

# 查询域名超时时间（可选）
# resolver_timeout 5s;
```



#### 反向代理

```bash
# 负载均衡，可以添加多个服务器，一般配合反代一起用
# upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
upstream tomcat_cms {
    ip_hash;
    server    127.0.0.1:8801 weight=3 max_fails=3 fail_timeout=100s;
    # server    127.0.0.1:8802 weight=2 max_fails=3 fail_timeout=100s;
    # server    127.0.0.1:8803 weight=1 max_fails=3 fail_timeout=100s;
}

location /  {
    # 设置主机头和客户端真实地址，以便服务器获取客户端真实IP
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header REMOTE-HOST $remote_addr;

    # WebSocket支持
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
    # proxy_connect_timeout 4s;
    # proxy_read_timeout 60s;
    # proxy_send_timeout 12s;

    # 禁用缓存，根据需要配
    # proxy_buffering off;

    # 错误自定义选项，由后端服务响应错误码设为off，由nginx响应错误信息设为on
    proxy_intercept_errors on;

    # 此项可配负载服务器，或直接写内部访问地址
    # proxy_pass  http://127.0.0.1:5212;
    proxy_pass  http://tomcat_cms;
}
```



#### Nginx 编译参数

```bash
# 生产环境建议编译参数：
./configure --user=www --group=www \
--prefix=/usr/local/nginx \
--with-http_stub_status_module --with-http_ssl_module \
--with-http_gzip_static_module --with-http_sub_module \
--with-pcre

# 常用编译参考参数：
./configure --user=nginx --group=nginx \
--prefix=/usr/local/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--with-http_stub_status_module --with-http_ssl_module \
--with-http_gzip_static_module --with-http_sub_module \
--with-http_v2_module --with-http_mp4_module --with-http_flv_module \
--http-client-body-temp-path=/var/tmp/nginx/client \
--http-proxy-temp-path=/var/tmp/nginx/proxy \
--http-fastcgi-temp-path=/var/tmp/nginx/fastcgi \
--with-pcre --with-debug

# 宝塔面板默认编译参数：
nginx version: nginx/1.16.1
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) 
built with OpenSSL 1.1.1e  17 Mar 2020
TLS SNI support enabled
configure arguments: --user=www --group=www --prefix=/www/server/nginx --add-module=/www/server/nginx/src/ngx_devel_kit --add-module=/www/server/nginx/src/lua_nginx_module --add-module=/www/server/nginx/src/ngx_cache_purge --add-module=/www/server/nginx/src/nginx-sticky-module --with-openssl=/www/server/nginx/src/openssl --with-pcre=pcre-8.43 --with-http_v2_module --with-stream --with-stream_ssl_module --with-stream_ssl_preread_module --with-http_stub_status_module --with-http_ssl_module --with-http_image_filter_module --with-http_gzip_static_module --with-http_gunzip_module --with-ipv6 --with-http_sub_module --with-http_flv_module --with-http_addition_module --with-http_realip_module --with-http_mp4_module --with-ld-opt=-Wl,-E --with-cc-opt=-Wno-error --with-ld-opt=-ljemalloc --with-http_dav_module --add-module=/www/server/nginx/src/nginx-dav-ext-module

# yum install nginx 使用的编译参数：
nginx version: nginx/1.10.3
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-23) (GCC) 
built with OpenSSL 1.0.1e-fips 11 Feb 2013
TLS SNI support enabled
configure arguments: --prefix=/usr/share/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/lib/nginx/tmp/client_body --http-proxy-temp-path=/var/lib/nginx/tmp/proxy --http-fastcgi-temp-path=/var/lib/nginx/tmp/fastcgi --http-uwsgi-temp-path=/var/lib/nginx/tmp/uwsgi --http-scgi-temp-path=/var/lib/nginx/tmp/scgi --pid-path=/var/run/nginx.pid --lock-path=/var/lock/subsys/nginx --user=nginx --group=nginx --with-file-aio --with-ipv6 --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_geoip_module=dynamic --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_slice_module --with-http_stub_status_module --with-http_perl_module=dynamic --with-mail=dynamic --with-mail_ssl_module --with-pcre --with-pcre-jit --with-stream=dynamic --with-stream_ssl_module --with-debug --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic' --with-ld-opt=' -Wl,-E'
```

