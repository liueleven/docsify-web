# Nginx常用配置（案例）

> 写于：2018-09-11

### 前言
Nginx作为一个中间件，一般可以作为反向代理，负载均衡，HTTP静态服务器，


### 反向代理配置(跨域访问)
跨域访问就可以这样处理，浏览器输入a.com nginx就会代理到a.com:8080，如果直接访问8080就会出现跨域
```
server {
    listen       80;
    server_name  a.com;

	location / {
	    proxy_pass http://a.com:8080/;
    }

    location /bcom/ {
	    proxy_pass http://b.com:8080/;
    }
}
```
### 负载均衡配置

配置负载均衡参考博客

https://blog.csdn.net/wangmx1993328/article/details/80903520
https://blog.csdn.net/wangmx1993328/article/details/80902483



```
http {
    # 这里只要加个upstream就可以了，就会采用轮询的方式访问你配置的服务器
    # 这里的localhost对应下方proxy_pass 中的地址
    upstream localhost{
        server 127.0.0.1:8080;
        server 127.0.0.1:8081;
        server 127.0.0.1:8082;
    }
    #upstream localhost{
    #    server 127.0.0.1:8080 weight=1;
    #    server 127.0.0.1:8081 weight=5;
    #    server 127.0.0.1:8082 weight=10;#权重配置访问优先级
    #    ip_hash; #一个ip固定访问一个服务器
    #    fair;      #哪个服务器响应时间短有限分配
    #}
    
    server {
        listen  80;
        server_name localhost;
        localhost / {
            proxy_pass http://localhost
        }
        
        #省略其它配置...
    }
}
```
再你的浏览器中输入 http://localhost它就会被转发到
server 127.0.0.1:8080;
        server 127.0.0.1:8081;
        server 127.0.0.1:8082;中的其中一个它是以轮询的方式进行转发的，当然如果8080这个服务器性能特别好，可以配置权重，数字越大优先访问。session统一也很简单，比如放在缓存中。也可以直接写上ip_hash那么这个固定的ip就访问指定的一台服务器了
        
        
### HTTP静态服务器
```
server{
        listen 9999;
        server_name localhost;  //这里有域名就更好了localhost->域名
        location / {
                root /home/**/manage;  //静态文件的路径
        }
}
```

### 动静请求分离
```
upstream test{  
       server localhost:8080;  
       server localhost:8081;  
    }   

    server {  
        listen       80;  
        server_name  localhost;  

        location / {  
            root   e:\wwwroot;  
            index  index.html;  
        }  

        # 所有静态请求都由nginx处理，存放目录为html  
        location ~ \.(gif|jpg|jpeg|png|bmp|swf|css|js)$ {  
            root    e:\wwwroot;  
        }  

        # 所有动态请求都转发给tomcat处理  
        location ~ \.(jsp|do)$ {  
            proxy_pass  http://test;  
        }  

        error_page   500 502 503 504  /50x.html;  
        location = /50x.html {  
            root   e:\wwwroot;  
        }  
    }  
```


### 图片服务器
访问localhost:8087/images 就可以访问到图片了，缓存 过期时间是一天
```
server {
        listen       8087;
        server_name  localhost;
        charset utf-8
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /images {
            alias /Users/imooc/imgs; # 文件目录
            expires 1d;
         #  root   html;
           #  index  index.html index.htm;
        }
```        