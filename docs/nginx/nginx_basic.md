# Nginx 

## Nginx简介： 
<!--nginx功能之一可以启动一个本地服务器，通过配置server_name和root目录等来访问目标文件 

尽管通过这种方式能实现 ==分布式文件存储== ，但也存在弊端，就是FTP很容易被入侵，而且小型的网站使用FTP作为文件服务器是没问题的，但是项目访问量持续增加的话，必要考虑文件服务器的扩展性与高可用，目前成熟的文件服务器也有很多，例如FastDFS,可以快速的进行线性扩容。-->
- **Nginx** (engine x) 是一个**高性能**的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。Nginx是由伊戈尔·赛索耶夫为俄罗斯访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。（有报告表明能支持高达**50000**个并发连接数）

- 其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布。  

- Nginx是一款**轻量级**的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是**占有内存少**，**并发能力强**，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、京东、新浪、网易、腾讯、淘宝等。

- Nginx支持**热部署**。它的启动特别容易, 并且几乎可以做到7*24不间断运行，即使运行数个月也不需要重新启动。你还能够在不间断服务的情况下，对软件版本进行进行升级。

---
## nginx下载地址： 
http://nginx.org/

---
## Nginx.conf的基本格式:

```conf
...              #全局块

events {         #events块
   ...
}

http      #http块
{
    ...   #http全局块
    server        #server块
    { 
        ...       #server全局块
        location [PATTERN]   #location块
        {
            ...
        }
        location [PATTERN] 
        {
            ...
        }
    }
    server
    {
      ...
    }
    ...     #http全局块
}
```
1. 全局块：配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。
2. events块：配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，开启多个网络连接序列化等。
3. http块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。
4. server块：配置虚拟主机的相关参数，一个http中可以有多个server。
5. location块：配置请求的路由，以及各种页面的处理情况。

---
## 启动Nginx

进入解压目录执行如下命令

```shell
start nginx : 启动nginx服务
nginx -s reload  ：修改配置后重新加载生效
nginx -s reopen  ：重新打开日志文件
nginx -t -c /path/to/nginx.conf 测试nginx配置文件是否正确
```

---
## Nginx常用命令

```shell
验证配置是否正确: nginx -t
查看Nginx的版本号：nginx -V
启动Nginx：start nginx
快速停止或关闭Nginx：nginx -s stop
正常停止或关闭Nginx：nginx -s quit
配置文件修改重装载命令：nginx -s reload
```

---
## Nginx常用功能 -- 反向代理  

Nginx可以根据不同的正则匹配，采取不同的转发策略，比如图片文件结尾的走文件服务器，动态页面走web服务器，只要你正则写的没问题，又有相对应的服务器解决方案，你就可以随心所欲的玩。并且Nginx对返回结果进行错误页跳转，异常判断等。如果被分发的服务器存在异常，他可以将请求重新转发给另外一台服务器，然后自动去除异常服务器。
- 正向代理是服务不知道请求者的具体身份
- 反向代理是请求者不知道自己请求的具体服务器

### 反向代理实例
只需要修改service块中的内容
```conf
...

http {
    
    ...
    
    server {
        # 监听的地址及端口
        listen       80;
        server_name  localhost;
    
        #charset koi8-r;
    
        #access_log  logs/host.access.log  main;
        
        # 不论哪个在上，都是执行更精确的location块
        # 路径中包含sessionTest的指向路径
        location ~ /sessionTest/  {
            proxy_pass   http://localhost:8080;
        }
        
        # 默认的指向路径
        location / {
            # 转发到的地址
            proxy_pass  http://localhost:8081;
        }
    }
}
```

---
## Nginx常用功能 -- 负载均衡

Nginx提供的负载均衡策略有2种：内置策略和扩展策略。内置策略为轮询，加权轮询，Ip hash。扩展策略，就天马行空，只有你想不到的没有他做不到的啦，你可以参照所有的负载均衡算法，给他一一找出来做下实现。

### 轮询（默认）
每个请求按时间顺序逐一分配的不同的后端服务器，如果后端服务器down掉，能自动剔除。
```conf
...

http {
    
    ...
    
    # 负载均衡服务列表
    upstream myserver {
        server localhost:8080;
        server localhost:8081;
    }
    
    server {
        # 监听的端口
        listen       80;
        
        # 监听的地址 多个域名之间用空格分开
        server_name  localhost;

        location / {
            # 转发到的地址 (服务列表名)
            proxy_pass  http://myserver;
        }
    }
}
```


### 加权轮询
权重越高分配到的客户端越多（权重默认为1）
```
    # 负载均衡服务列表
    upstream myserver {
        server localhost:8080 weight=1;
        server localhost:8081 weight=2;
    }
```

### ip hash
Ip hash算法，对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一台服务器进行处理，可以解决session不共享的问题。
```
    # 负载均衡服务列表
    upstream myserver {
        # ip_hash 模式
        id_hash;
        server localhost:8080;
        server localhost:8081;
    }
```

### fair(第三方)
根据响应时间来分配。
```
    # 负载均衡服务列表
    upstream myserver {
        server localhost:8080;
        server localhost:8081;
        # fair 模式
        fair;
    }
```

---
## Nginx常用功能 -- 动静分离
为了加快网站的解析速度，可以发动态页面和静态页面由不同的服务器来解析，加快解析速度，降低单个服务器的压力。 (分离的是请求)

```
...

http {
    
    ...
    
    server {
        # 监听的端口
        listen       80;
        
        # 监听的地址 多个域名之间用空格分开
        server_name  localhost;

        # 指向的是nginx的文件目录
        location /static/  {
            root    /data/;

            # 设置目录浏览
            autoindex   on;
            
            # 默认为on，显示出文件的确切大小，单位是bytes。
            # 改为off后，显示出文件的大概大小，单位是kB或者MB或者GB
            autoindex_exact_size off;
            
            # 默认为off，显示的文件时间为GMT时间。
            # 改为on后，显示的文件时间为文件的服务器时间
            autoindex_localtime on;
        }
        
        # 默认的指向路径
        location / {
            # 转发到的地址
            proxy_pass  http://localhost:8081;
        }
    }
}
```

---
## Nginx常用功能 -- web缓存
Nginx可以对不同的文件做不同的缓存处理，配置灵活，并且支持FastCGI_Cache，主要用于对FastCGI的动态程序进行缓存。配合着第三方的ngx_cache_purge，对制定的URL缓存内容可以的进行增删管理。
expires : 设置浏览器缓存时间，减少与服务器的请求和流量（适用于不经常变动的资源），
在有效期间，会与服务区文件最后更新时间进行对比，若无变化则不会从服务器抓取。

```
...

http {
    
    ...
    
    server {
        # 监听的地址及端口
        listen       80;
        server_name  localhost;
    
        #charset koi8-r;
    
        #access_log  logs/host.access.log  main;
        
        # 不论哪个在上，都是执行更精确的location块
        # 路径中包含sessionTest的指向路径
        location ~ /sessionTest/  {
            proxy_pass   http://localhost:8080;
        }
        
        # 默认的指向路径
        location ~ \.(wma|wmv|asf|mp3|mmf|zip|rar|swf|flv)$ {
           root /var/www/upload/;
           expires 3d;
       }
    }
}
```
---

## Nginx配置高可用集群
nginx宕机后，依然可以正常执行请求（nginx集群）

#### 准备工作
- 需要两台服务器
- 在两台服务器都安装nginx
- 在两台服务器上安装keepalived

#### keepalived配置文件
主备都需要进行如下配置：

##### 全局定义部分
```
global_defs {
    notification_email {
        acassen@firewall.loc
        failover@firewall.loc
        sysadmin@firewall.loc
    }
    notification_email_from Alexandre.Cassen@firewall.loc
    smtp_server 192.168.200.1
    smtp_connect_timeout 30
    router_id LVS_DEVEL
}
```
##### 实例定义
```
vrrp_instance VI_1 {
    # 主服务器设置为MASTER 备份服务器设置为BACKUP
    state MASTER
    # 网卡
    interface eth0
    # 主备机路由 必须一致
    virtual_router_id 51
    # 主备机优先级 主机就设置的稍微大一点
    priority 100
    # 1s发送一次心跳
    advert_int 1
    # 权限方式 （密码）
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    # 虚拟ip
    virtual_ipaddress {
        192.168.200.16
    }
}
```
##### 脚本部分
```
vrrp_script chk_http_port {
  # 脚本位置
  script "/usr/local/src/nginx_check.sh"
  # 脚本检测执行间隔时间
  interval 2
  # 设置当前服务器的权重
  weight 2
}
```

###### nginx 脚本文件 nginx_check.sh
脚本内容如下，需要放置到上述脚本位置
主服务器挂掉之后，杀死keepalived，备服务器替代主服务器
```
#!/bin/bash
A=`ps -C nginx -no-header |wc -l`
if [ $A -eq 0 ];then
    /user/local/nginx/sbin/nginx
    sleep 2
    if [ `ps -C nginx --no-header |wc -l` -eq 0 ]; then
        killall keepalived
    fi
fi
```

##### 启动keepalived
```
systemctl start keepalived.service
```
---

## Nginx 工作原理
- [Nginx 工作原理 -- 项思凯](https://www.cnblogs.com/xiangsikai/p/8438772.html)