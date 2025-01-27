# 幂等性

幂等的 HTTP 方法，**同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。**

GET，HEAD，PUT 和 DELETE 等方法都是幂等的，

而POST 方法不是。所有的安全方法也都是幂等的。

# GET和POST的区别？

- GET 被强制服务器支持

- 浏览器对URL的长度有限制，所以GET请求不能代替POST请求发送大量数据

- GET请求发送数据更小

- GET请求是**不安全的**

- GET请求是**幂等的**

	- 幂等的意味着对同一URL的多个请求应该返回同样的结果
	
- POST请求不能被缓存

- POST请求相对GET请求是「安全」的

	- 这里安全的含义仅仅是指是非修改信息

- GET用于信息获取，而且是安全的和幂等的

	- 所谓安全的意味着该操作用于获取信息而非修改信息。换句话说，GET 请求一般不应产生副作用。就是说，它仅仅是获取资源信息，就像数据库查询一样，不会修改，增加数据，不会影响资源的状态。

- POST是用于修改服务器上的资源的请求

- 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

## 我们从表面上来看看GET和POST的区别：

- GET是从服务器上**获取数据**，POST是向服务器**传送数据**。 GET和 POST只是一种传递数据的方式，GET也可以把数据传到服务器，他们的本质都是发送请求和接收结果。只是组织格式和数据量上面有差别，http协议里面有介绍

- GET是把参数数据队列加到提交表单的ACTION属性所指的URL中，值和表单内各个字段一一对应，在URL中可以看到。POST是通过HTTP POST机制，将表单内各个字段与其内容放置在HTML HEADER内一起传送到ACTION属性所指的URL地址。用户看不到这个过程。 因为GET设计成传输小数据，而且最好是不修改服务器的数据，所以浏览器一般都在地址栏里面可以看到，但POST一般都用来传递大数据，或比较隐私的数据，所以在地址栏看不到，能不能看到不是协议规定，是浏览器规定的。

- 对于GET方式，服务器端用Request.QueryString获取变量的值，对于POST方式，服务器端用Request.Form获取提交的数据。 没明白，怎么获得变量和你的服务器有关，和GET或POST无关，服务器都对这些请求做了封装

- GET传送的**数据量较小**，不能大于2KB。POST传送的数据量较大，一般被默认为不受限制。但理论上，IIS4中最大量为80KB，IIS5中为100KB。 - POST**基本没有限制**，我想大家都上传过文件，都是用POST方式的。只不过要修改form里面的那个type参数

- GET**安全性非常低**，POST**安全性较高**。 如果没有加密，他们安全级别都是一样的，随便一个监听器都可以把所有的数据监听到。

作者：stormjun94(微信公众号同号）
https://www.nowcoder.com/discuss/216466?type=2来源：牛客网

| Post一般用于更新或者添加资源信息 | Get一般用于查询操作，而且应该是安全和幂等的 |
| -------------------------------- | ------------------------------------------- |
| Post更加安全                     | Get会把请求的信息放到URL的后面              |
| Post传输量一般无大小限制         | Get不能大于2KB                              |
| Post执行效率低                   | Get执行效率略高                             |

### 为什么POST效率低，Get效率高

- Get将参数拼成URL,放到header消息头里传递
- Post直接以键值对的形式放到消息体中传递。
- 但两者的效率差距很小很小