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

<p>{{ page.date | date_to_string }}</p>
