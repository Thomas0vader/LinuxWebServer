

TinyWebServer
===============
Linux下C++轻量级Web服务器，助力初学者快速实践网络编程，搭建属于自己的服务器.

* 使用 **线程池 + 非阻塞socket + epoll(ET和LT均实现) + 事件处理(Reactor和模拟Proactor均实现)** 的并发模型
* 使用**状态机**解析HTTP请求报文，支持解析**GET和POST**请求
* 访问服务器数据库实现web端用户**注册、登录**功能，可以请求服务器**图片和视频文件**
* 实现**同步/异步日志系统**，记录服务器运行状态
* 经Webbench压力测试可以实现**上万的并发连接**数据交换

目录
-----

| [概述](#概述) | [框架](#框架) | [Demo演示](#Demo演示) | [压力测试](#压力测试) | [运行](#运行) | [个性化运行](#个性化运行) 


概述
----------

> * C/C++
> * B/S模型
> * [线程同步机制包装类]
> * [http连接请求处理类]
> * [半同步/半反应堆线程池]
> * [定时器处理非活动连接]
> * [同步/异步日志系统 ]
> * [数据库连接池]
> * [同步线程注册和登录校验]
> * [简易服务器压力测试]


框架
-------------
<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0j1atq5hj30g60lm0w4.jpg" height="765"/> </div>

Demo演示
----------
> * 注册演示

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0iz0dkleg30m80bxjyj.gif" height="429"/> </div>

> * 登录演示

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0izcc0r1g30m80bxn6a.gif" height="429"/> </div>

> * 请求图片文件演示(6M)

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0juxrnlfg30go07x4qr.gif" height="429"/> </div>

> * 请求视频文件演示(39M)

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1ge0jtxie8ng30go07xb2b.gif" height="429"/> </div>


压力测试
-------------
在关闭日志后，使用Webbench对服务器进行压力测试，对listenfd和connfd分别采用ET和LT模式，均可实现上万的并发连接，下面列出的是两者组合后的测试结果. 

> * Proactor，LT + LT，93251 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjqu2hptkj30gz07474n.jpg" height="201"/> </div>

> * Proactor，LT + ET，97459 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr1xppdgj30h206zdg6.jpg" height="201"/> </div>

> * Proactor，ET + LT，80498 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr24vmjtj30gz0720t3.jpg" height="201"/> </div>

> * Proactor，ET + ET，92167 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjrflrebdj30gz06z0t3.jpg" height="201"/> </div>

> * Reactor，LT + ET，69175 QPS

<div align=center><img src="http://ww1.sinaimg.cn/large/005TJ2c7ly1gfjr1humcbj30h207474n.jpg" height="201"/> </div>

> * 并发连接总数：10500
> * 访问服务器时间：5s
> * 所有访问均成功


运行
------------
* 服务器测试环境
	* Ubuntu版本16.04
	* MySQL版本5.7.29
* 浏览器测试环境
	* Windows、Linux均可
	* Chrome
	* FireFox
	* 其他浏览器暂无测试

* 测试前确认已安装MySQL数据库

    ```C++
    // 建立yourdb库
    create database yourdb;

    // 创建user表
    USE yourdb;
    CREATE TABLE user(
        username char(50) NULL,
        passwd char(50) NULL
    )ENGINE=InnoDB;

    // 添加数据
    INSERT INTO user(username, passwd) VALUES('name', 'passwd');
    ```

* 修改main.cpp中的数据库初始化信息

    ```C++
    //数据库登录名,密码,库名
    string user = "root";
    string passwd = "root";
    string databasename = "yourdb";
    ```

* build

    ```C++
    sh ./build.sh
    ```

* 启动server

    ```C++
    ./server
    ```

* 浏览器端

    ```C++
    ip:9006
    ```

个性化运行
------

```C++
./server [-p port] [-l LOGWrite] [-m TRIGMode] [-o OPT_LINGER] [-s sql_num] [-t thread_num] [-c close_log] [-a actor_model]
```

温馨提示:以上参数不是非必须，不用全部使用，根据个人情况搭配选用即可.

* -p，自定义端口号
	* 默认9006
* -l，选择日志写入方式，默认同步写入
	* 0，同步写入
	* 1，异步写入
* -m，listenfd和connfd的模式组合，默认使用LT + LT
	* 0，表示使用LT + LT
	* 1，表示使用LT + ET
    * 2，表示使用ET + LT
    * 3，表示使用ET + ET
* -o，优雅关闭连接，默认不使用
	* 0，不使用
	* 1，使用
* -s，数据库连接数量
	* 默认为8
* -t，线程数量
	* 默认为8
* -c，关闭日志，默认打开
	* 0，打开日志
	* 1，关闭日志
* -a，选择反应堆模型，默认Proactor
	* 0，Proactor模型
	* 1，Reactor模型

测试示例命令与含义

```C++
./server -p 9007 -l 1 -m 0 -o 1 -s 10 -t 10 -c 1 -a 1
```



致谢
------------
Linux高性能服务器编程，游双著.


