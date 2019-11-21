## http协议

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1573982860-image-20191109220352266.png)

### 第一章网络协议分层

 ![image-20191109221555591](https://raw.githubusercontent.com/Nobodylesszb/upic/pic/upload/pics/image-20191109221555591.png?token=AJI2HRV4YKREYVSHJQVP2MK52DUNM)

- 物理层主要作用是定义物理设备如何传输数据
- 数据链路层在通讯的实体间建立数据链路连接
- 网络层为数据在结点之间传输逻辑链路
- 传输层
  - 提供端到端的服务
  - 传输层向高层屏蔽了下层数据通信的细节
- 应用层
  - 为应用软件提供很多服务
  - 构建于tcp协议之上

### HTTP三次握手

- 在一个tcp连接中可以发送多个http请求
- 三次握手主要为了规避因为网络问题而导致了服务端开销问题

### url uri urn

- url uniform resource locator 统一资源定位器
- Urn 永久统一资源定位符

### http报文

![](https://cdn.jsdelivr.net/gh/Nobodylesszb/upic@pic/upload/pics/1573982615-image-20191109232743746.png)

http方法对应对于资源的操作

httpcode 可以通过httpcode来判断结果

### cors预请求

在跨域的时候只允许get，head，post方法，这三个方法是不需要预请求的

允许的content-type也有限制

- text/plain
- Mutipart/form-data
- Application/x-www-form-urlencoded
- 这三个content-type不需要预请求，其他的都需要预请求

其他的显示

- 请求头限制
- xmlhttprequestupload对象均没有注册任何时间监听器
- 请求中没有使用readblestream对象

**什么是预请求**

- 在跨域的情况下。为了保证服务器的安全，对于一些除了上述的请求几种方式。其他的请求都需要预先进行一次options的预请求来保证请求是安全的

### cache-control

#### 可缓存性

- public
  - 在http响应返回的过程中，在cache-control中设置这个值，代表这个http请求它返回的内容所经过的任何路径当中包括中间代理服务器，以及发出这个请求的浏览器客户端可以进行对这个返回内容的缓存
- private
  - 代表只有发起请求的这个浏览器才可以缓存
- no-cache
  - 任何一个节点都不可以缓存
  - 是指在本地进行一个缓存，在代理服务器进行一个缓存，但是在每一次请求的时候都需要去服务器验证一下，如果服务器返回这个请求告诉你可以使用本地缓存，才可以真正的去使用这个本地缓存

#### 到期

指的是这个缓存什么时候到期

max-age = <seconds>

s-maxage=<seconds>

- 代理服务器端的设置，如果客户端的max-age 和s-max都设置了。优先使用代理服务器端的s-maxage

max-stale = <seconds>

- 在我们的max-age过期之后，如果我们返回的资源中有max-stale这个设置

- max-stale 表示即使浏览器缓存已经失效，如果在max-stale这个时间内，依然使用过期缓存，不需要去原服务器请求

#### 重新验证

must-revalidate

- 在我们设置max-age这个缓存到期时间后，如果缓存已经过期了。我们必须去原服务端发起请求重新获得数据，再来验证这个数据是否真的过期了，而不能直接使用本地缓存

proxy-revalidate

- 用于缓存服务器上，如果缓存已经过期了。我们必须去原服务端发起请求重新获得数据，再来验证这个数据是否真的过期了，而不能直接使用本地缓存

#### 其他

no-store

- 和no-cache一个区分
- no-store 是指本地和节点都不缓存，永远都要去服务端拿新的数据

no-transfrom

- 用于代理服务器，告诉代理服务器不要随意改动数据格式，

#### 注意

**这些头它只是限制性说明性的，没有强制作用**

#### 资源验证

#### ![资源验证](https://i.loli.net/2019/11/16/VrIa1hDG7O8J43i.png)

**数据如何验证**

主要通过http两个验证头

1. last-modified
   1. 配合if-modified-since或者if-unmodified-since使用
   2. 如果我们请求一个资源，在我们请求这个资源响应返回header里面有last-modified这个头指定了一个时间，在下次请求这个资源的时候，就会带上这个值通过if-modified-since带到服务器上，服务器来检验这个资源最后修改日期，如果发现这两个时间是一样的，这就标明这个资源未被修改，客户端可以直接使用缓存

2. etag
   1. 主要通过数据签名来验证资源，配合if-match,if-non-match使用
   2. 如果我们请求一个资源，在我们请求这个资源响应返回header里面有etag这个头给定了一个签名，在下次请求这个资源的时候，就会带上这个值通过if-mathc带到服务器上来对比是否使用缓存

#### Cookie和session

Max-age 和expires设置过期时间

secure只在https的时候发送

httponly无法通过js的document.cookie来访问