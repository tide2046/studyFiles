# HTTP协议 #
刚刚说到其实协议就是让浏览器跟服务器互相知道他们要什么，因此浏览器发给服务器的包就要带着一些用户操作信息，服务器发给浏览器的包就要带着用户想要的数据信息。  
当浏览器输入一个URL，相当于浏览器发起一个HTTP请求包出去，浏览器会把一些自身信息以及用户操作的信息写在包头上带到服务器，这样服务器才知道你在用什么浏览器想请求它的什么资源。

## HTTP请求包的常见包头如下
GET / HTTP/1.1  
获取的路径以及HTTP版本  
Host: www.qq.com:8080  
服务器主机名字&端口号  
Connection: keep-alive  
用于长连  
User-Agent: Chrome/35.0.1916.153  
浏览器基本信息  
Accept-Encoding: gzip,deflate,sdch  
告诉服务器支持的编码  
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,nl;q=0.4,zh-TW;q=0.2  
告诉服务器优先支持的语言  
Cookie: id=1;username=raphealguo;  
解决HTTP无状态重要的Cookie，里边是key=value对  紧接着服务器收到HTTP请求，经过CGI处理之后回复一个HTTP响应包，服务器需要告诉你包里边是什么（Content-Type），包里边有多少东西  （Content-Length），服务器版本是什么等等。  

## HTTP响应包常见的包头如下：  

HTTP/1.1 200 OK   
HTTP版本以及状态码  
Server: nginx/1.4.1  
服务器版本  
Date: Mon, 30 Jun 2014 09:44:10 GMT  
回包时间  
Content-Type: text/html; charset=UTF-8  
包里边的类型  
Content-Length: 14534  
包体的长度  
Connection: keep-alive  
用于长连  
Cache-Control: no-cache, must-revalidate  
用于缓存控制  
具体的HTTP协议的细节推荐阅读《HTTP权威指南》。  