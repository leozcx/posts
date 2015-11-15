
使用angularjs实现客户端登陆管理
=============
在使用web应用时，第一步恐怕就是登陆了；在多数情况下，登陆之前用户只能做一些查看之类基本的操作，如果想要做修改之类的操作就必须要登陆了；以一个blog应用为例，不登陆用户只查看博客，登陆之后，视用户类别，可以做新增，删除博客，评论，删除评论等操作。通常，登陆之前和之后用户看到的界面也是不同的，有些特定的按钮，链接只有在登陆之后才可见。这就要求：
1. 客户端需要了解用户登陆了没有，并且不能因为刷新页面，或在新的tab中打开链接，甚至重新打开浏览器而丢失用户登陆信息；
2. 服务器端也需要知道用户登陆了没有。HTTP是无状态的，不能要求用户每个请求都要提供用户名密码。
那么，如何解决这两个问题呢？

## 客户端
客户端代码需要知道用户的信息，比如用户名等，并且要在切换页面，打开新标签页的时候依然能读取这些信息，就需要将信息保存在浏览器中；有多种技术可以实现这一功能： sesstionStorage vs localStorage vs cookies
### cookies
[cookies](http://www.allaboutcookies.org) 是历史比较悠久的在客户端保存数据的技术，以key-value的方式保存信息。cookies信息会被包含在每个HTTP头中，通常由server端设置，与cookies相关的HTTP头有2个： Set-Cookie 和 Cookie。Set-Cookie由server端设置在响应头中，用于告诉浏览器创建cookie；Cookie由client端设置，放在请求头中，浏览器发送请求时会检查请求的URL，根据domain及path是否匹配来决定是否设置cookie。
Set-Cookie的格式如下：
```
Set-Cookie: <name>=<value>[; <name>=<value>]...
[; expires=<date>][; domain=<domain_name>]
[; path=<some_path>][; secure][; httponly]
```
Set-Cookie头包含key-value对，多对以分号分隔；如此之外，还可包含以下几个可选信息： 
- expires=<date> 表示cookie的过期时间，以如下格式设置：DAY, DD-MMM-YYYY HH:MM:SS GMT，比如expires=Fri, 31-Dec-2010 23:59:59 GMT。注意，不是我们常用的用一串数字表示时间。如果省略则页面关闭cookie就会被清除。  
 - domain和path用来告诉浏览器什么时候需要发送cookie。比如`domain="abc.web.com"` 则只有用户请求“abc.web.com"的时候cookie才被设置；`domain="web.com"` 则告诉浏览器请求"web.com"及所有子域名的时候都要设置cookie；`domain="abc.web.com"; path="images"`， 只有在请求"abc.web.com/images" 的时候才设置cookie 
- secure 如果存在，则只有请求https网页的时候才会设置；
- httponly 表示该cookie只被用于http请求，即js代码无法读取该cookie

Cookies的格式很简单，就是key-value对：
```
Cookie: <name>=<value> [;<name>=<value>]...
```
那么如何来设置cookie呢？通常是由server端来设的，即在响应头中设置Set-Cookie。一般的语言都有相应的设置方法，这里不多说。还可以在浏览器端用js来设置，一般的前端框架也有相应的方法，具体可以查看对应文档。


## 服务器端

> Written with [StackEdit](https://stackedit.io/).
> http://jerryzou.com/posts/cookie-and-web-storage/
> http://diveintohtml5.info/storage.html
> http://code.tutsplus.com/tutorials/an-introduction-to-cookies--net-12482