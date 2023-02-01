# WebSocket

HTTP是无状态无连接的单向请求协议，如果想获取服务器状态的更新，需要轮询，效率低且消耗资源。

WebSocket是伴随HTML5产生的一种新协议，通过一次简单的握手就可以建立用户端和服务器连接，服务器根据业务规则可以主动推送信息给用户端，实现了客户端与服务器全双工通信。其优点如下：

- 不需要多次创建TCP请求和销毁，节约宽带和服务器的资源
- 数据格式比较轻量，性能开销小，通信高效
- 可以发送文本，也可以发送二进制数据
- 没有同源限制，客户端可以与任意服务器通信

WSS（Web Socket Secure）是 WebSocket 的加密版本，WSS下不支持ip地址的写法，需要写成域名形式

WebSocket与HTTP协议有着良好的兼容性，使用相同的TCP端口，即80和443。

WebSocket在建立握手时，数据是通过HTTP传输的。但是建立之后，在真正传输时候是不需要HTTP协议的。

## websocket-client

websocket-client is a WebSocket client for Python

`pip install websocket-client`

- 项目地址：<https://github.com/websocket-client/websocket-client>
- 示例：<https://websocket-client.readthedocs.io/en/latest/examples.html>

大多数教程中使用的测试链接已经停止服务：`ws://echo.websocket.org`，有人搭建了新的替代服务：`ws://echo.websocket.events`（UI界面: <https://echo.websocket.events/.ws>），也可以自己搭建：<https://github.com/jmalloc/echo-server>

### 短连接

```python
# 创建连接，方式1
# import websocket
# # websocket.enableTrace(True)  # 查看详细连接信息：request/response header、Sent/Rcv raw/decoded
# ws = websocket.WebSocket()
# ws.connect("ws://echo.websocket.events")


# 创建连接，方式2
from websocket import create_connection
ws = create_connection("ws://echo.websocket.events/")

print(ws.getstatus())  # 获取连接状态

print("Sending...")
ws.send("Hello, World")  # 发送请求

result =  ws.recv()  # 返回结果
print("Received '%s'" % result)

ws.close()  # 断开连接


# 输出结果
"""
101
Sending...
Received 'echo.websocket.events sponsored by Lob.com'
"""
```

使用with自动关闭消息

```python
from contextlib import closing
from websocket import create_connection

with closing(create_connection("wss://testnet-explorer.binance.org/ws/block")) as conn:
    conn.send("Hello, World")
    print(conn.recv())
```

### 长连接

```python
import json
import ssl
import sys
import time
from threading import Thread

import websocket

payload = json.dumps({
    "op": "subscribe",
    "args": [{
        "channel": "mark-price",
        "instId": "BTC-USDT"
    }]
})


# 用于接收消息
def on_message(ws, message):
    print(message)


# 用于接收错误异常
def on_error(ws, error):
    print(error)


# 用于关闭连接
def on_close(ws, close_status_code, close_msg):
    print("### closed ###")


# 用于保持连接
def on_open(ws):
    def run(*args):
        for i in range(3):
            ws.send("ping")
            time.sleep(1)

        ws.send(payload)
        time.sleep(1)
        ws.close()
        print("Thread terminating...")
    Thread(target=run).start()



if __name__ == "__main__":
    # websocket.enableTrace(True)  # 显示详细信息
    host = "wss://wspap.okx.com:8443/ws/v5/public?brokerId=9999" if len(sys.argv) < 2 else sys.argv[1]
    ws = websocket.WebSocketApp(
        host,
        on_message=on_message,
        on_error=on_error,
        on_close=on_close,
        # header=headers,
    )
    ws.on_open = on_open
    ws.run_forever(
        sslopt={"cert_reqs": ssl.CERT_NONE},  # 禁用ssl
    )
```

## websockets

websocket-client只能实现客户端，如果要创建一个 WebSocket 服务器，则需要使用 `websockets`

> <https://websockets.readthedocs.io/>

`pip install websockets`
