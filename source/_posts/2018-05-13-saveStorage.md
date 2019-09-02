---
title: Cookie LocalStorage sessionStorage 
date: 2018-05-13 22:00:04
tags: 浏览器缓存
categories: 
	- 浏览器缓存
---

Cookie/LocalStorage/essionStorage 
=============

一、数据的生命周期  
Cookie 一般由服务器生成，可设置失效时间，如果在浏览器端生成Cookie，默认是关闭浏览器后失效  
LocalStorage 除非被清除，否则永远保存  
sessionStorage 仅在当前会话下有效，关闭页面或浏览器后被清除  

二、存放数据大小  
Cookie `4K` 左右  
LocalStorage  一般为 `5MB `   
sessionStorage  一般为 `5MB`  

三、与服务器端通信   
Cookie 每次都会携带在HTTP头中，如果使用Cookie 保存过多数据会带来性问题  
LocalStorage  sessionStorage  仅在客户端（浏览器）中保存，不参与和服务器的通信  

四、易用性  
Cookie 需要程序员自己封装，源生的Cookie接口不友好  
LocalStorage  sessionStorage  源生接口可以接受，亦可再次封装来对Object和Array有更好的支持  

LocalStorage/lobalStorage/sessionStorage/Cookie/Session
=============
△  
1.`Localstorage` 是 `html5`存储数据的方式，在HTML5中，本地存储是一个`window`的属性，包括`localStorage`和`sessionStorage`.HTML5本地存储只能存字符串，任何格式存储的时候都会被自动转为字符串，所以读取的时候，需要自己进行类型的转换。是永久性存储，当然用户可以通过浏览器设置来删除

2.`globalStorage` 目的是跨越会话存储数据，不过要指定哪些域可以访问该数据，如果不使用`removeItem（）`或`delete`删除，或者用户未清除浏览器缓存，存储在`globalStorage`上的数据会一直保留在磁盘上，所以`globalStorage`非常适合在`客户端存储文档`或者`长期保留用户偏好设置`

3.`localStorage`在`HTML5`中作为保持客户端数据的方案取代`globalStorage`，它不能被指定访问规则，要访问localStorage，页面必须来自 **同一个域名，使用同一种协议，在同一个端口上**，它的数据也保留到通过JavaScript删除或用户清除浏览器缓存

4.`Cookie` 是存在浏览器端，每次浏览器发送请求都会携带cookie信息，如果是第一次请求的话就是没有Cookie。Cookie是可以通过域名下面的页面共享，不同域名下面的Cookie不共享。父级目录下面的cookie，子目录是可以获取到的，但是子级上的cookie，父级获取不到。cookie是有过去的时间的，一般默认的关闭时间是页面关闭的时间，时间是可以自己设置的。cookie的格式是一个字符串，是一个键值对的形式。cookie是有大小限制的，一般是4K，如果数据太大的话，会影响传递速度或是效率。`Document.cookie`可以来设置获取cookie的信息。

5.`Session` 存储于服务端，依赖于`cookie`，服务端相当于有一个session存储区，没有大小的限制

Web存储（localStorage与sessionStorage）
=============

目的：让客户端可以存储数据。  
区别：  
 `localStorage` 除非人为地删除，否则存储的数据将被用久保存下来   `sessionStorage` 存储的数据仅在浏览器的一次会话中有效，当结束会话时，数据被请除。

以localStorage为例：

```javascript
if(window.localStorage){
    console.log("支持web存储标准");
}
else{
    console.log("不支持web存储标准");
}
```

方法与属性：
1. `setItem('key','data') `通过该方法对数据进行存储，它接受两个参数，键名、要存储的数据。  
>localStorage.setItem('key','data');  
sessionStorage.setItem('key','data');
2. `getItem()` 通过该方法获取以及存储的数据，它接受一个参数，对用的键名
>localStorage.getItem('key');
sessionStorage.getItem('key');
3. `removeItem()` 通过该方法删除某条已经存储的数据，它接受一个参数：对用的键名
>localStorage.removeItem('key');  
sessionStorage.removeItem('key');  
4. `clear()` 通过该方法可以清除所有已经存储的数据
>localStorage.clear();  
sessionStorage.clear();  
5. ` key()` 通过该方法可以获得已存储的数据的键名，它接受一个参数，数值索引，与该数组相似，该索引从下标为0开始
>localStorage.key(2);  
sessionStorage.key(2);  
6.  `length`   唯一的属性，用过该属性可以得知已存储的数据数量
>localStorage.length ();  
sessionStorage.length ();  

Cookie/session/localStorage的区别 
==================================

`cookie`的内容主要包括：名字、值、过期时间、路径和域。路径与域一起构成cookie的作用范围。若不设置时间，则表示这个cookie的生命期为浏览器会话期间，关闭浏览器窗口，cookie就会消失。这种生命期为浏览器会话期的cookie被称为`会话cookie`。   
`会话cookie`一般不存储在硬盘而是`保存在内存`里，当然这个行为并不是规范规定的。若设置了过期时间，浏览器就会把cookie保存到硬盘上，关闭后再打开浏览器这些cookie仍然有效直到超过设定的过期时间。对于保存在内存里的cookie，不同的浏览器有不同的处理方式session机制。   
当程序需要为某个客户端的请求创建一个`session`时，服务器首先检查这个客户端的请求里是否已包含了一个`session标识（称为session id）`，如果已包含则说明以前已经为此客户端创建过`session`，服务器就按照`session id`把这个`session`检索出来使用（检索不到，会新建一个），如果客户端请求不包含`session id`，则为客户端创建一个`session`并且生成一个与此`session`相关联的`session id`，`session id`的值应该是一个既不会重复，又不容易被找到规律以仿造的字符串，这个session id将被在本次响应中返回给客户端保存。保存这个`session id`的方式可以采用`cookie`，这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器。

cookie/session的区别：
================================== 
1、`cookie`数据存放在客户的`浏览器`上，`session`数据放在`服务器`上   
2、`cookie`不是很安全，别人可以分析存放在本地的cookie并进行cookie欺骗，考虑到`安全`应当使用`session `  
3、`session`会在一定时间内保存在服务器上，当访问增多，会比较占用你服务器的性能，考虑到`减轻服务器性能`方面，应当使用`cookie`   
4、单个cookie保存的数据不能超过`4K`，很多浏览器都限制一个站点最多保存`20个`cookie   
5、建议将`登录信息`等重要信息存放为`session`，`其他信息`如果需要保留，可以放在`cookie`中   
6、`session`保存在`服务器`，客户端不知道其中的信息；`cookie`保存在`客户端`，服务器能够知道其中的信息   
7、`session`中保存的是`对象`，`cookie`中保存的是`字符串`   
8、`session`不能区分路径，同一个用户在访问一个网站期间，所有的session在任何一个地方都可以访问到，而`cookie`中如果设置了`路径参数`，那么同一个网站中不同路径下的cookie互相是访问不到的


浏览器本地存储与服务器端存储的区别 
================================== 
其实数据既可以在浏览器本地存储，也可以在服务器端存储 
浏览器可以保存一些数据，需要的时候直接从本地存取，`sessionStorage`、`localStorage`和`cookie`都是由`浏览器`存储在本地的数据 
`服务器端`也可以保存所有用户的所有数据，但需要的时候浏览器要向服务器请求数据。   
1、服务器端可以保存用户的持久数据，如数据库和云存储将用户的大量数据保存在服务器端   
2、服务器端也可以保存用户的临时会话数据，服务器端的session机制，如jsp的session对象，数据保存在服务器上  

实际上，`服务器`和`浏览器`之间仅需传递`session id`即可，`服务器`根据`session id`找到对应用户的`session对象`，会话数据仅在`一段时间`内有效，这个时间就是server端设置的`session有效期`

`服务器端`保存所有的用户的数据，所以服务器端的开销较大，而浏览器端保存则把不同用户需要的数据分别保存在用户各自的浏览器中，`浏览器端`一般只用来`存储小数据`，而非服务可以存储大数据或小数据服务器存储数据安全一些，浏览器只适合存储一般数据

sessionStorage/localStorage/cookie的区别 
================================== 
共同点：都是保存在`浏览器端`、且`同源`的 
区别：   
1、`cookie`数据始终在`同源的http请求中携带`（即使不需要），即 **cookie在浏览器和服务器间来回传递**，而`sessionStorage`和`localStorage`不会自动把数据发送给服务器，仅在`本地保存`。`cookie`数据还有路径 `path` 的概念，可以限制`cookie`只属于某个路径下   
2、存储大小限制也不同，`cookie`数据不能超过`4K`，同时因为每次http请求都会携带cookie、所以cookie只适合保存很小的数据，如会话标识。`sessionStorage`和`localStorage`虽然也有存储大小的限制，但比cookie大得多，可以达到`5M或更大`   
3、数据有效期不同
`sessionStorage`：仅在当前浏览器窗口关闭之前有效  
`localStorage`：`始终有效`，窗口或浏览器关闭也一直保存，因此用作持久数据  
`cookie`：只在设置的cookie`过期时间`之前有效，即使窗口关闭或浏览器关闭     
4、作用域不同
`sessionStorage`不在不同的浏览器窗口中共享  
即使是同一个页面`localstorage`在所有同源窗口中都是共享的  
`cookie`也是在所有同源窗口中都是共享的   
5、web Storage支持事件通知机制，可以将数据更新的通知发送给监听者   
6、web Storage的api接口使用更方便  

sessionStorage与页面js数据对象的区别 
================================== 
页面中一般的js对象的生存期仅在当前页面有效，因此刷新页面或转到另一页面这样的重新加载页面的情况，数据就不存在了   
而sessionStorage只要同源的同窗口中，刷新页面或进入同源的不同页面，数据始终存在，也就是说只要浏览器不关闭，数据仍然存在   