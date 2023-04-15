---
title: Javascript - 关于 Cookie、Session、Localstorage
tags: 
 -  Web
---



<!-- ![cover](https://images.unsplash.com/photo-1556468282-52b7aa97e00a?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80) -->

<!-- more -->


### Cookie、Session、Localstorage



#### Cookie：

- "小饼干" - 即其存储大小很小，仅为4K
- 主要用途为 保存登录信息、用户状态等，并用以记录页面或浏览器关闭刷新之后仍需记录保存的数据
- 查看方式：document.cookie
- 通常由Server 生成(Set-cookie)，可设置失效时间。
- **在同源http请求中是始终会携带着的**，一定程度上影响请求速度。
- Client中可以访问修改。以字符串的方式保存在客户端。



#### Cookie的用法：

```html
//Server设置发送的HTTP头：
Set-Cookie: <cookie-name>=<cookie-value>;(可选参数1);(可选参数2)

//Client设置Cookie:
document.cookie = "<cookie-name>=<cookie-value>;(可选参数1);(可选参数2)"

//可选参数有：
Expires=<date>：cookie的最长有效时间，若不设置则cookie生命期与会话期相同
Max-Age=<non-zero-digit>：cookie生成后失效的秒数
Domain=<domain-value>：指定cookie可以送达的主机域名，若一级域名设置了则二级域名也能获取。
Path=<path-value>：指定一个URL，例如指定path=/docs，则”/docs”、”/docs/Web/“、”/docs/Web/Http”均满足匹配条件
Secure：必须在请求使用SSL或HTTPS协议的时候cookie才回被发送到服务器
HttpOnly：客户端无法更改Cookie，客户端设置cookie时不能使用这个参数，一般是服务器端使用
```



#### Session:

- 大小为5MB
- 在无状态的HTTP协议下，服务端记录用户状态时用于标识具体用户的机制
- 会话级别的存储，也就是说**仅在当前会话下有效，关闭页面或浏览器后被清除**(因刷新后session_id改变了)

- 一定时间内存储在Server中，访问增多时，会影响Server性能。
- 服务器使用Session过程：
  - 检查Client请求中是否有session_id, 有则查找相关信息，无则创建(不重复的session_id)
  - session_id 加载cookie中，并在响应请求中返回给Client保存
- 以对象的方式保存在服务器端。
- 当客户端禁止cookie时，可url中传递session_id(sid=xxxx)，由此仍可以判断是哪个用户，而不需要客户端的信息。





#### Localstorage

- 大小为5MB甚至更大
- 可永久保存在客户端中，除非手动删除
- 不参与与服务器的通信/请求
- 相比于cookie ，拥有更好的API接口(removeItem,setItem,clear)



#### 三者的区别：



| 特性           | Cookie                                                       | localStorage                                                | sessionStorage                                              |
| -------------- | ------------------------------------------------------------ | ----------------------------------------------------------- | ----------------------------------------------------------- |
| 数据的生命期   | 一般由服务器生成，可设置失效时间。如果在浏览器端生成Cookie，默认是关闭浏览器后失效 | 除非被清除，否则永久保存                                    | 仅在当前会话下有效，关闭页面或浏览器后被清除                |
| 存放数据大小   | 4K左右                                                       | 一般为5MB                                                   | 一般为5MB                                                   |
| 与服务器端通信 | 每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题 | 仅在客户端（即浏览器）中保存，不参与和服务器的通信          | 仅在客户端（即浏览器）中保存，不参与和服务器的通信          |
| 易用性         | 需要程序员自己封装，源生的Cookie接口不友好                   | 源生接口可以接受，亦可再次封装来对Object和Array有更好的支持 | 源生接口可以接受，亦可再次封装来对Object和Array有更好的支持 |
| 存储方式       | 键值对                                                       | 键值对                                                      | 键值对                                                      |



### 使用场景：



- 可用cookie 来记录用户简单的登录信息
- 可用seesion来记录用户多个表单或多个页面连续性填写的信息。
- 可用localstorag 来存储用户其他大型数据，例如购物车、收藏等信息。





#### 参考：

- [详说 Cookie, LocalStorage 与 SessionStorage](https://jerryzou.com/posts/cookie-and-web-storage/)
- [细说localStorage, sessionStorage, Cookie, Session](https://juejin.im/entry/5ac4d661f265da23a049c92a)
- [彻底理解cookie,session,localStorage(附代码)](https://www.jianshu.com/p/ca8637b05c8a)