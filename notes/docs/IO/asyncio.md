# asyncio

标准库：<https://docs.python.org/3/library/asyncio.html>

```python
import asyncio

async def main():
    print('Hello ...')
    await asyncio.sleep(1)
    print('... World!')

asyncio.run(main())
```
