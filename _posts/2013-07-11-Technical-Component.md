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

# */examples/jsp下的所有文件均缓存5秒钟
    if (req.url ~ "/examples/jsp") {
        set beresp.ttl = 5s;
    }
}
</pre>

## logback_slf4j   ##

## sitemesh        ##

## redis           ##
key-value store, 
## jedis           ##

## dal             ##


<p>{{ page.date | date_to_string }}</p>
