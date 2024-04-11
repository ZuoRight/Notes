# Python 请求库

参考：<https://medium.com/@louis.rosevi/python-web-sockets-5-different-ways-5ffb1a9015f9>

## websockets

> <https://websockets.readthedocs.io>

`pip install websockets`

### server

```python
import asyncio
import websockets

"""
1. 编写异步任务处理函数 handler
2. 创建 server 对象
3. 异步运行 server 对象
4. 启动服务端：python server.py
"""

"""
handler基础写法
客户端断开连接时，服务端会引发ConnectionClosedOK异常
异常信息会对服务端日志产生噪音
"""
# async def handler(websocket):
#     while True:
#         message = await websocket.recv()
#         print(message)
"""
handler改进写法，捕获异常
"""
# async def handler(websocket):
#     while True:
#         try:
#             message = await websocket.recv()
#         except websockets.ConnectionClosedOK:
#             break
#         print(message)
"""
handler最优写法
websockets 提供了快捷方式接收信息，无需自己处理异常
"""
async def handler(websocket):
    async for message in websocket:
        print(message)


async def main():
    """
    main() 协程调用 serve() 来启动 Websockets 服务器
    创建一个 Future 可等待对象，暂停协程，等待 Future 对象完成
    但没有任何机制设置其完成状态（比如 set_result 或 set_exception），所以将永远处于等待状态
    即 WebSockets 服务会持续运行，直到程序被外部信号终止
    """
    async with websockets.serve(handler, "localhost", 8001):
        await asyncio.Future()


if __name__ == "__main__":
    """入口点：创建一个 asyncio 事件循环，运行 main() 协程，然后关闭循环"""
    asyncio.run(main())
```

### client

- 命令行

```shell
python -m websockets ws://localhost:8001
```

- 代码实现

```python
import asyncio
import websockets

"""
1. 建立与服务器的连接：connect()
2. 收发消息：recv(), send()
3. 关闭连接：close()
"""

async def hello():
    uri = "ws://localhost:8001"
    async with websockets.connect(uri) as websocket:
        await websocket.send("hello")

        # res = await websocket.recv()
        # print(res)

if __name__ == "__main__":
    asyncio.run(hello())
```

## websocket-client

主要用于创建 WebSocket 客户端，且不支持异步，即不兼容 `aioredis` 等异步库

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
