## 1.正向代理和反向代理的区别<img src="/Users/gzqy/Library/Application Support/typora-user-images/image-20191118105159350.png" alt="image-20191118105159350" style="zoom:50%;" />

#### 正向代理
	由于防火墙的原因，我们并不能直接访问谷歌，那么我们可以借助VPN来实现，这就是一个简单的正向代理的例子。这里你能够发现，正向代理“代理”的是客户端，而且客户端是知道目标的，而目标是不知道客户端是通过VPN访问的。
### 反向代理
	当我们在外网访问百度的时候，其实会进行一个转发，代理到内网去，这就是所谓的反向代理，即反向代理“代理”的是服务器端，而且这一个过程对于客户端而言是透明的。
## 安装nginx（Mac端）
1. 先安装homebrew
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

2. 安装nginx
	brew install nginx
3. 遇到问题：
	1. 已经安装过，重装或者链接
	2. 8080端口被占用，关闭占用进程
		
		```
		losf -i:端口号			查看占用端口进程
		kill 进程号			关闭进程
	```
		
	3. 8080端口被占用，通过修改权限去更改nginx端配置文件，修改端口为其他未占用端口如8181
4. Nginx常用命令
``` nginx  #启动nginx
nginx -s stop #快速停止nginx
nginx -s quit  #从容停止nginx
nginx -V #查看版本，以及配置文件地址
nginx -v #查看版本
nginx -s reload|reopen|stop|quit   #重新加载配置|重启|快速停止|安全关闭nginx
nginx -h #帮助
killall nginx #停止所有nginx进程
systemctl restart nginx.service # 重启nginx服务
```
## 负载均衡
在upstream配置中编写被代理的服务器地址并配置负载均衡算法
```
//写法一：
upstream mysvr { 
    server 192.168.10.121:3333;
    server 192.168.10.122:3333;
}
//写法二：
server {
    ....
    location  ~*^.+$ {         
        proxy_pass  http://mysvr;  #请求转向mysvr 定义的服务器列表         
    }
}
```
1. 热备：如果你有2台服务器，当一台服务器发生事故时，才启用第二台服务器给提供服务。服务器处理请求的顺序：AAAAAA突然A挂啦，BBBBBBBBBBBBBB.....
```
upstream mysvr { 
    server 127.0.0.1:7878; 
    server 192.168.10.121:3333 backup;  #热备     
}
```
2. 轮询：nginx默认就是轮询其权重都默认为1，服务器处理请求的顺序：ABABABABAB...
```
upstream mysvr { 
    server 127.0.0.1:7878;
    server 192.168.10.121:3333;       
}
```
3. 加权轮询：跟据配置的权重的大小而分发给不同服务器不同数量的请求。如果不设置，则默认为1。下面服务器的请求顺序为：ABBABBABBABBABB....
```
upstream mysvr { 
    server 127.0.0.1:7878 weight=1;
    server 192.168.10.121:3333 weight=2;
}
```
4. ip_hash方式，将相同IP的客户端的所有请求都发给相同的服务器
```
upstream mysvr { 
    server 127.0.0.1:7878 weight=2 max_fails=2 fail_timeout=2;
    server 192.168.10.121:3333 weight=1 max_fails=2 fail_timeout=1;    
}
```

## 参照默认ngin.conf.default配置文件写自己的配置文件

1. 定义错误页error_page 404  /404_error.html;`

2. 简单限制访问

   ```
   nginx
   location /{
   	deny 123.2.2.2; #禁止123.2.2.2访问
   	allow 222.222.222.222; #允许222.222.222.222访问
   }
   
   ```
3. 复杂限制访问
	```
	   nginx
   location ～\.php$ { #通过一个正则来匹配，如例为匹配以.php结尾的文件遵循以下规则
   	deny 123.2.2.2; #禁止123.2.2.2访问
   	allow 222.222.222.222; #允许222.222.222.222访问
   }
	```
4. 基于ip、端口配置虚拟主机
5. 基于域名配置虚拟主机
	```
	server {
    listen       8080;
    server_name  [域名1];
    }
	```
	```
	server{
        listen 80;
        server_name [域名2];
        location / {
                root /usr/share/nginx/html/html8001;
                index index.html index.htm;
        }
}
	```
6.简单反向代理
```nginx
#创建文件
mkdir /data/public/
chmod 777 /data/public/
配置nginx.conf
worker_processes 1;
error_log logs/error.log info;
events {
    use epoll;
}
http {
    server {
        # 监听 8080 端口
        listen 8080;
        location /share/ {
            # 打开自动列表功能，通常关闭
            autoindex on;
            # 将 /share/ 路径映射至 /data/public/，请保证 nginx 进程有权限访问 /data/public/
            alias /data/public/;
        }
    }
}
```
7. 适配pc和移动端
8. 配置不同版本时http和https
9. 设置访问方式仅限get或者post

