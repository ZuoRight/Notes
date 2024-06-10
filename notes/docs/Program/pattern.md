# 设计模式

![20240608200150](https://image.zuoright.com/20240608200150.png)

图片来源：极客时间《设计模式之美》

## 事件驱动

基于对事件的响应进行操作，事件可以是用户操作（如点击、输入）、系统事件（如文件更改、消息到达）等，系统持续监听各种事件，当事件发生时，调用相应的处理程序。比如在消息队列中通过事件驱动实现消息的发布和订阅，事件驱动系统通常是异步的，可以处理并发的多个事件

- JS 中处理按钮点击事件

```js
document.getElementById("myButton").addEventListener("click", function() {
    alert("Button clicked!");
});
```

- Python 中异步

```python
import asyncio

async def handle_event():
    print("Event handled!")

async def main():
    print("Waiting for event...")
    await asyncio.sleep(2)
    await handle_event()

asyncio.run(main())
```

## 关键字驱动

一种自动化测试方法，它使用预定义的关键字来描述测试操作。每个关键字对应一种操作或测试步骤，测试用例通过这些关键字来组合和实现。

```python
# 定义关键字
def open_browser(url):
    print(f"Opening browser with URL: {url}")

def verify_title(expected_title):
    print(f"Verifying that the title is: {expected_title}")

# 使用关键字编写测试用例
def test_case():
    open_browser("https://example.com")
    verify_title("Example Domain")

test_case()
```
