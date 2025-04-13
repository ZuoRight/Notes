# WebSocket

TCP 协议虽然是全双工的，但 HTTP 协议是半双工的，同一时间只能一端给另一端发数据

很多时候服务端需要向客户端主动发送数据，即推送，比如扫码登陆，很长一段时间都是通过定时轮询（特定时间向服务器请求数据）或长轮询等方式来实现的，但这些并不是真正意义上的推送，不仅浪费资源且效率低下。

这是由于当时设计时未考虑像网页游戏这样的场景，于是基于 TCP 的全双工协议 WebSocket 伴随着 HTML5 应运而生。

使用 WebSocket 协议需要先发送一个 HTTP 协议，请求头中带上特殊的字段，然后升级为 WS 协议

![20240612192609](https://image.zuoright.com/20240612192609.png)

因此有人会说 WebSocket 是建立在 HTTP 之上的，其实只是建立连接时用到了 HTTP，升级到 WS 后便与 HTTP 没有任何关系了

WebSocket 完美继承了 TCP 的全双工能力，又解决了粘包问题，适用于客户端与服务器频繁交互的大部分场景，比如网页聊天室、网页或小程序游戏、证券交易等

其优点如下：

- 不需要多次创建 TCP 请求和销毁，节约宽带和服务器的资源
- 数据格式比较轻量，性能开销小，通信高效
- 可以发送文本，也可以发送二进制数据
- 没有同源限制，客户端可以与任意服务器通信

WebSocket 与 HTTP 协议有着良好的兼容性，使用相同的TCP端口，即 `80` 和 `443`。

WSS（Web Socket Secure）是 WebSocket 的加密版本，WSS 下不支持 ip 地址的写法，需要写成域名形式

- 请求

![20240410233609](https://image.zuoright.com/20240410233609.png)

- 返回

![20240410233621](https://image.zuoright.com/20240410233621.png)

---

最初网站为了实现推送技术，都是通过 Ajax 轮训，即以特定时间间隔（比如1s）对服务器发出 HTTP 请求，十分浪费带宽资源。

HTML5 推出后，浏览器可通过 JavaScript 向服务器发出建立 WebSocket 连接的请求，连接建立以后，客户端和服务器端就可以通过 TCP 连接直接交换数据。

![20240410231603](https://image.zuoright.com/20240410231603.png)

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="utf-8">
    <title>demo</title>
    <script type="text/javascript">
        function WebSocketTest() {           
            // 打开一个 web socket
            var ws = new WebSocket("ws://localhost:8001");
            ws.onopen = function() {
                // Web Socket 已连接上，使用 send() 方法发送数据
                ws.send("发送数据");
                alert("数据发送中...");
            };
                        
            ws.onmessage = function (evt) { 
                var received_msg = evt.data;
                alert("数据已接收...");
            };
                        
            ws.onclose = function() { 
                // 关闭 websocket
                alert("连接已关闭..."); 
            };
        }
      </script>  
</head>
<body>
    <div id="sse">
        <a href="javascript:WebSocketTest()">运行 WebSocket</a>
    </div>
</body>
</html>

```
