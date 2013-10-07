---
layout: master
title: eCommerce Technical Component
---
# {{ page.title }} #


## jquery          ##

## java_exception  ##

## varnish         ##
[varnish offical web site](https://www.varnish-cache.org/)
[varnish official book](https://www.varnish-software.com/static/book/)

配置文件目录
> /usr/local/etc/varnish/default.vcl

可执行文件目录
> /usr/local/sbin/varnishd  -f /usr/local/etc/varnish/default.vcl  -s malloc,1G -T 127.0.0.1:2000 -a 0.0.0.0:80

管理工具目录
> /usr/local/bin/varnish*

<pre class="brush:jsp">
	//当session打开（=true）时，varnish将不缓存，直接访问backend server
	<%@ page session="false"%>
</pre>

<pre class="brush:perl">
# 添加一个backend server
backend default {
    .host = "127.0.0.1";
    .port = "8080";
}

sub vcl_fetch {
#    if (beresp.ttl <= 0s ||
#        beresp.http.Set-Cookie ||
#        beresp.http.Vary == "*") {
#               /*
#                * Mark as "Hit-For-Pass" for the next 2 minutes
#                */
#               set beresp.ttl = 10 s;
#               return (hit_for_pass);
#    }
#    return (deliver);
#     if (req.request != "GET" && req.request != "HEAD") {
#         /* We only deal with GET and HEAD by default */
#         return (pass);
#     }

# */examples/jsp下的所有文件均缓存5秒钟,虽然请求HTTP头中有Cookie信息
# 可以应用于浏览器频繁刷新页面，但是数据变化并不频繁
     if (req.http.Cookie && req.url ~ "/examples/jsp" ) {
         return (lookup);
     }

}

sub vcl_fetch {
    if (beresp.ttl <= 0s ||
        beresp.http.Set-Cookie ||
        beresp.http.Vary == "*") {
                /*
                 * 虽然后端返回了Cookie，但是仍然缓存10秒
                 * （缺省情况下varnish是不缓存的，而且2分钟内不再检查
                 * （return(hit_for_pass)））
                 */
                set beresp.ttl = 10 s;
                if (req.url ~ "/examples/jsp") {
#                    对于/examples/jsp下的页面缓存 5 秒
                        set beresp.ttl = 5s;
                }
                return (deliver);
    }

# 对于关闭了Session（不生成Cookie，session=false）的/examples/servlets下的页面
# 缓存 30 秒
    if(req.url ~ "/examples/servlets") {
        set beresp.ttl = 30 s;
    }
    return (deliver);
}


</pre>

## logback_slf4j   ##

## sitemesh        ##

## redis           ##
key-value store, 
## jedis           ##

## dal             ##

## Kafka           ##

## Node.js         ##

## Storm           ##

## D3              ##

## nagios          ##

## zookeeper       ##
## puppet  ##
##  scribe  ##

## storm ##

安装twitter storm集群组件ZeroMQ，jzmq时遇到的一系列问题
2人收藏此文章, 我要收藏 发表于1年前(2012-03-09 01:43) , 已有1400次阅读 ，共0个评论

最近在学习twitter storm 实时计算框架时遇到的一些小问题，在安装完storm,zookeeper,ZeroMQ之后，在安装jzmq时出现了一些小问题，经过认真的分析思考+查阅英文，终于解决这些很头疼的小问题。

问题一场景:（直接在linux上git clone jzmq源码的请略过，看后面有更多你期待的问题，Jump ->”问题二“以后）

执行./autogen.sh脚本后报如下异常：

-bash: ./autogen.sh: /bin/sh^M: bad interpreter: No such file or directory 

分享一下产生此问题的原因：我的git安装在本地（系windows），git clone jzmq的项目代码后，又利用ssh客户端上传到linux客户机器上，在linux上执行

cd  jzmq 

./autogen.sh

报如下异常：-bash: ./autogen.sh: /bin/sh^M: bad interpreter: No such file or directory 

分析：这是由于不同系统编码格式引起的：在windows系统中编辑的.sh文件可能有不可见字符，所以在Linux系统下执行会报以上异常信息。 

解决（直接在linux上git clone 的请略过，看后面有更多你期待的问题，Jump ->”问题二“以后哦）：

首先要确保文件有可执行权限 

#sh>chmod  a+x ./autogen.sh

然后修改文件格式 

#sh>vim  autogen.sh

输入如下命令查看文件格式 

:set ff 或 :set fileformat 

可以看到如下信息：

fileformat=dos 或 fileformat=unix 

利用如下命令修改文件格式 

:set ff=unix 或 :set fileformat=unix 

:wq (存盘退出) 

最后再执行文件 

#sh>./autogen.sh

ok！

问题二场景：

再次运行./autogen.sh 报缺少：pkg-config工具

wget  http://pkgconfig.freedesktop.org/releases/pkg-config-0.23.tar.gz

 tar  zxf  pkg-config-0.23.tar.gz

 cd  pkg-config-0.23

./configure --prefix=/usr/local/pkg-config-0.23 --datarootdir=/usr/share

 make

 sudo make install

安装完成后设置PATH：（后面要用到pkg-config）

 export  PATH=.:/usr/local/pkg-config-0.23/bin:$PATH

（注意：请选择pkg-config-0.23.tar.gz或之前版本安装，我选择了0.25或0.26最新版本make是折腾了很久通不过，请不要再重复掉到这个坑里，pkg-config-0.23之前版本的安装也是有些小陷阱的，请参阅下面）

小陷阱——安装 pkg-config<=0.23需要注意的地方：

./configure  --prefix=/usr/local/pkg-config-0.23  --datarootdir=/usr/share

--prefix=/usr/local/pkg-config-0.23指定pkg-config安装路径，这不是重点；

重点是--datarootdir=/usr/share它直接关系到你后面能否成功编译jzmq.它指明了pkg.m4将要存放的位置，jzmq在编译的过程中需要调用 PKG_CHECK_MODULES() 宏（Macro），这个Macro是pkg-config和Autoconf/Automake/aclocal交互的主要接口

参阅英文资料：

The main interface between autoconf and pkg-config is the PKG_CHECK_MODULES macro, which provides a very basic and easy way to check for the presence of a given package in the system. Nonetheless, there are some caveats that require attention when using the macro.
大概意思就是：
autoconf和pkg-config的之间的主要的交互接口是通过PKG_CHECK_MODULES宏，它提供了一个非常基本的和简单的方法来检查系统中的一个给定的包是否存在。然而使用宏时，也有一些需要注意的事项。
语法：

1
PKG_CHECK_MODULES(prefix, list-of-modules, action-if-found, action-if-not-found)
参数的意思参阅：

01
prefix
02
Each call to PKG_CHECK_MODULES should have a different prefix value (with a few exceptions discussed later on). This value, usually provided in uppercase, is used as prefix to the variables holding the compiler flags and libraries reported by pkg-config.
03
 
04
For instance, if your prefix was to be FOO you'll be provided two variables FOO_CFLAGS and FOO_LIBS.
05
 
06
This will also be used as message during the configure checks: checking for FOO....
07
 
08
list-of-modules
09
A single call to the macro can check for the presence of one or more packages; you'll see later how to make good use of this feature. Each entry in the list can have a version comparison specifier, with the same syntax as the Requires keyword in the data files themselves.
10
 
11
action-if-found, action-if-not-found
12
As most of the original autoconf macros, there are boolean values provided, for the cases when the check succeeded or failed. In contrast with almost all of the original macros, though, the default action-if-not-fault will end the execution with an error for not having found the dependency.
本例中生成的pkg.m4文件应该存在于 /usr/share/aclocal下。这个关系到autoconf和pkg-config的之间通过CALL PKG_CHECK_MODULES宏来检查给定依赖包是否存在，否则在编译JZMQ时将会报告”Syntax error  ./configure: line 15272:  PKG_CHECK_MODULES(' ` ".

01
cd   ~/jzmq
02
 
03
./configure 
04
 
05
$ ........ chechking  for  .......
06
 
07
ok,success!
08
 
09
$ make
10
 
11
...
12
 
13
make[1]: *** No rule to make target `classdist_noinst.stamp', needed by `org/zeromq/ZMQ.class'.  Stop.
14
 
15
make: *** [all-recursive] Error 1
 然后,touch “classdist_noinst.stamp”：

1
$ touch src/classdist_noinst.stamp
2
$ make
3
...
4
make[1]: *** No rule to make target `org/zeromq/ZMQException.class, needed by `all'.  Stop.
5
make: *** [all-recursive] Error 1
 然后, 编译class：

1
$ cd src/org/zeromq/
2
$ /jzmq/src/org/zeromq]$ javac  *.java
3
$ cd ..
4
$ make
5
...  success!
6
$ sudo make install


so then jzmq has installed  successfully and enjoy  it yourself！


<p>{{ page.date | date_to_string }}</p>


