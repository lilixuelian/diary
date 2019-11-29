### http协议是无状态的
很久很久以前，Web 基本上就是文档的浏览而已， 既然是浏览，作为服务器， 不需要记录谁在某一段时间里都浏览了什么文档，每次请求都是一个新的HTTP协议， 就是请求加响应，  尤其是我不用记住是谁刚刚发了HTTP请求，   每个请求对我来说都是全新的。这一次请求和上一次请求是没有任何关系的，互不认识的，没有关联的。这种无状态的的好处是快速。 
但是随着交互式Web应用的兴起，像在线购物网站，需要登录的网站等等，马上就面临一个问题，那就是要管理会话，必须记住哪些人登录系统，  哪些人往自己的购物车中放商品，  也就是说我必须把每个人区分开，这就是一个不小的挑战，因为HTTP请求是无状态的。
所以想出的办法就是给大家发一个会话标识(session id), 说白了就是一个随机的字串，每个人收到的都不一样，  每次大家向我发起HTTP请求的时候，把这个字符串给一并捎过来，这样就能区分开谁是谁了

#### 使用session存在的问题
这样大家很嗨皮了，可是服务器就不嗨皮了，每个人只需要保存自己的session id，而服务器要保存所有人的session id ！  如果访问服务器多了， 就得由成千上万，甚至几十万个。
这对服务器说是一个巨大的开销 ， 严重的限制了服务器扩展能力， 比如说我用两个机器组成了一个集群， 小F通过机器A登录了系统，  那session id会保存在机器A上，  假设小F的下一次请求被转发到机器B怎么办？  机器B可没有小F的 session id啊。

1、session sticky ， 就是让小F的请求一直粘连在机器A上， 但是这也不管用， 要是机器A挂掉了， 还得转到机器B去。
2、那只好做session 的复制了， 把session id  在两个机器之间搬来搬去， 快累死了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191128192707606.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)
3、把session id 集中存储到一个地方， 所有的机器都来访问这个地方的数据， 这样一来，就不用复制了， 但是增加了单点失败的可能性， 要是那个负责session 的机器挂了，  所有人都得重新登录一遍， 估计得被人骂死。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191128192720307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)
　　　　　　   
4、尝试把这个单点的机器也搞出集群，增加可靠性， 但不管如何， 这小小的session 对我来说是一个沉重的负担
怎样解决？ 
于是有人就一直在思考， 我为什么要保存这可恶的session呢， 只让每个客户端去保存该多好？
 
可是如果不保存这些session id ,  怎么验证客户端发给我的session id 的确是我生成的呢？  如果不去验证，我们都不知道他们是不是合法登录的用户， 那些不怀好意的家伙们就可以伪造session id , 为所欲为了。
 
嗯，对了，关键点就是验证 ！
 
比如说， 小F已经登录了系统， 我给他发一个令牌(token)， 里边包含了小F的 user id， 下一次小F 再次通过Http 请求访问我的时候， 把这个token 通过Http header 带过来不就可以了。
 
不过这和session id没有本质区别啊， 任何人都可以可以伪造，  所以我得想点儿办法， 让别人伪造不了。
 
那就对数据做一个签名吧， 比如说我用HMAC-SHA256 算法，加上一个只有我才知道的密钥，  对数据做一个签名， 把这个签名和数据一起作为token ，   由于密钥别人不知道， 就无法伪造token了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191128192728642.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg3MDEyNw==,size_16,color_FFFFFF,t_70)
 
这个token 我不保存，  当小F把这个token 给我发过来的时候，我再用同样的HMAC-SHA256 算法和同样的密钥，对数据再计算一次签名， 和token 中的签名做个比较， 如果相同， 我就知道小F已经登录过了，并且可以直接取到小F的user id ,  如果不相同， 数据部分肯定被人篡改过， 我就告诉发送者： 对不起，没有认证。
## token和session的区别：
功能是一样的，都是要与浏览器建立连接，获取与客户端对应的用户数据，只不过完成这个功能的实现方式不太一样。
### 本质上的区别：
**session**的使用方式是客户端cookie里存id，服务端session存用户数据，客户端访问服务端的时候，根据id找用户数据。
**token**的使用方式是客户端里存id（也就是token）、用户信息、密文，服务端什么也不存，服务端只有一段加密代码，用来判断当前加密后的密文是否和客户端传递过来的密文一致，如果不一致，就是客户端的用户数据被篡改了，如果一致，就代表客户端的用户数据正常且正确。
### 流程：
#### session：
```mermaid
flowchat
st=>start: 注册登录
e=>end: 再次访问时根据cookie里的sessionid找到session里的user
a=>start: 服务端将user存入session
b=>start: 客户端将sessionid存入浏览器的cookie
st->a->b->e
```
#### token:
```mermaid
flowchat
st=>start: 注册登录
e=>end: 后台会再次使用token+user生成新密文，与传递过来的密文比较，一致则正确
a=>start: 服务端将生成一个token，并将token与user加密生成一个密文
b=>start: 将token+user+密文数据 返回给浏览器
c=>start: 再次访问时传递token+user+密文数据
st->a->b->c->e
```
注：上文中得token里保存的用户信息，一般不会包含敏感信息。

## cookie的其他应用场景：
[cookie的其他应用场景](https://www.cnblogs.com/lingyejun/p/9282169.html)
[http协议无状态中的 "状态" 到底指的是什么？！](https://www.cnblogs.com/bellkosmos/p/5237146.html)