# WS 请求库

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
