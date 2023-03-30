# aiohttp

aiohttp 是基于 asyncio 实现的第三方HTTP框架：<https://docs.aiohttp.org/en/stable/>

`pip install aiohttp`

```python
import aiohttp
import asyncio

async def main(): 
    async with aiohttp.ClientSession() as session: 
            async with session.get("https://example.com") as response:
            # async with session.post("http://httpbin.org/post", data={"key": "value"}) as response:
                print(await response.text())

asyncio.run(main())
```
