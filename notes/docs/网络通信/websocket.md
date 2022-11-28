# WebSocket

WebSocket是HTML5一种新的协议。它实现了用户端与服务器全双工通信，使得数据可以快速地双向传播。通过一次简单的握手就可以建立用户端和服务器连接，服务器根据业务规则可以主动推送信息给用户端。其优点如下：

- 用户端和服务器进行数据传输时，请求头信息比较小，大概2个字节。
- 用户端和服务器皆可以主动地发送数据给对方。
- 不需要多次创建TCP请求和销毁，节约宽带和服务器的资源。

`pip install websocket-client`

- 请求

```python
# 创建连接
websocket.create_connection("ws://127.0.0.1:5000/info")

# 获取连接状态
ws.getstatus()

# 发送请求
ws.send('ping')

# 返回结果
result = ws.recv()

# 断开连接
ws.close()
```

- 订阅

```python
import websocket
from threading import Thread
import time
import sys
 
def on_message(ws, message):
    print(message)  # 服务器返回消息
 
def on_error(ws, error):
    print(error)  # 连接异常信息
 
def on_close(ws):
    print("连接关闭")


def on_open(ws):
    def run(*args):
    for i in range(3):
        ws.send("Hello %d" % i)
        time.sleep(1)
 
    time.sleep(1)
    ws.close()
    print("Thread terminating...")
 
  Thread(target=run).start()
 
 
if __name__ == "__main__":
    websocket.enableTrace(True)
    host = "ws://echo.websocket.org/"
    ws = websocket.WebSocketApp(
        host,
        on_message=on_message,
        on_error=on_error,
        on_close=on_close
    )
    ws.on_open = on_open
    ws.run_forever()
```
