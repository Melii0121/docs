# 1.请解释一下什么是Nginx?

- Nginx---Ngine X，是一款免费的、自由的、开源的、高性能HTTP服务器和反向代理服务器；也是一个IMAP、POP3、SMTP代理服务器；Nginx以其高性能、稳定性、丰富的功能、简单的配置和低资源消耗而闻名。

- 也就是说Nginx本身就可以托管网站（类似于Tomcat一样），进行Http服务处理，也可以作为反向代理服务器 、负载均衡器和HTTP缓存。

- Nginx 解决了服务器的C10K（就是在一秒之内连接客户端的数目为10k即1万）问题。它的设计不像传统的服务器那样使用线程处理请求，而是一个更加高级的机制—事件驱动机制，是一种异步事件驱动结构。

---
# 2.请列举Nginx的一些特性

- 跨平台：可以在大多数Unix like 系统编译运行。而且也有Windows的移植版本。 

- 配置异常简单：非常的简单，易上手。 

- 非阻塞、高并发连接：数据复制时，磁盘I/O的第一阶段是非阻塞的。官方测试能支持5万并发连接，实际生产中能跑2~3万并发连接数（得益于Nginx采用了最新的epoll事件处理模型（消息队列）。 

- Nginx代理和后端Web服务器间无需长连接； 

- Nginx接收用户请求是异步的，即先将用户请求全部接收下来，再一次性发送到后端Web服务器，极大减轻后端Web服务器的压力。 

- 发送响应报文时，是边接收来自后端Web服务器的数据，边发送给客户端。 

- 网络依赖性低，理论上只要能够ping通就可以实施负载均衡，而且可以有效区分内网、外网流量。 

- 支持内置服务器检测。Nginx能够根据应用服务器处理页面返回的状态码、超时信息等检测服务器是否出现故障，并及时返回错误的请求重新提交到其它节点上。 

- 此外还有内存消耗小、成本低廉（比F5硬件负载均衡器廉价太多）、节省带宽、稳定性高等特点。

---
# 3.请列举Nginx和Apache 之间的不同点

Nginx | apache
---|---
Nginx是一个基于事件的web服务器 | Apache是一个基于流程的服务器
所有请求都由一个线程处理 | 单个线程处理单个请求
Nginx避免子进程概念 | Apache基于子进程
Nginx类似与速度 | Apache类似于功率
Apache在内存消耗和连接方面比较好 | Apache在内存消耗和连接方面没有提高
Nginx在负载均衡方面比较好 | 当流量到达进程极限时，Apache将拒绝新的连接
Nginx不支持INBMi和OpenVMS一样的OS | Apache支持更多的OS
Nginx只有核心功能 | Apache提供了更多的功能
Nginx的性能和可伸缩性不依赖于硬件 | Apache依赖于CPU和内存等硬件组件

---
# 4.请解释Nginx如何处理HTTP请求
Nginx 是一个高性能的 Web 服务器，能够同时处理大量的并发请求。它结合多进程机制和异步机制 ，异步机制使用的是异步非阻塞方式 ，接下来就给大家介绍一下 Nginx 的多线程机制和异步非阻塞机制 。


### 1. 多进程机制

服务器每当收到一个客户端时，就有 服务器主进程 （ master process ）生成一个 子进程（ worker process ）出来和客户端建立连接进行交互，直到连接断开，该子进程就结束了。

使用进程的好处是各个进程之间相互独立，不需要加锁，减少了使用锁对性能造成影响，同时降低编程的复杂度，降低开发成本。其次，采用独立的进程，可以让进程互相之间不会影响 ，如果一个进程发生异常退出时，其它进程正常工作， master 进程则很快启动新的 worker 进程，确保服务不会中断，从而将风险降到最低。

缺点是操作系统生成一个子进程需要进行 内存复制等操作，在资源和时间上会产生一定的开销。当有大量请求时，会导致系统性能下降 。

### 2. 异步非阻塞机制

每个工作进程 使用 异步非阻塞方式 ，可以处理 多个客户端请求 。

当某个 工作进程 接收到客户端的请求以后，调用 IO 进行处理，如果不能立即得到结果，就去 处理其他请求 （即为 非阻塞 ）；而 客户端 在此期间也 无需等待响应 ，可以去处理其他事情（即为 异步 ）。

当 IO 返回时，就会通知此 工作进程 ；该进程得到通知，暂时 挂起 当前处理的事务去 响应客户端请求 。