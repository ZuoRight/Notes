# Webhook

比如通过 Webhook 触发 Slack 工作流

又或者触发 Gitlab 流水线，然后将结果发送到钉钉

## trigger

```python
webhooks = f"https://code.example.org/api/v4/projects/344/ref/{ref_name}/trigger/pipeline?token={token}"
variables = f"&variables[APITEST_APP]={app}&variables[APITEST_ENV]={env}"
url = webhooks + variables
```

## 通知

- 钉钉：添加自定义机器人
- Slack：添加 Incoming webhook 应用

```python
import requests

def alert(text):
    webhook = f"https://api.dingtalk.com/robot/send?access_token=xxx"

    header = {
        "Content-Type": "application/json",
        "Charset": "UTF-8"
    }

    paylaod ={
        "msgtype": "markdown",
        "markdown": {
            "title": "测试结果",
            "text": text
        },
        "at": {
            "isAtAll": False
        }
    }

    r = requests.post(url=webhook, json=paylaod, headers=header)
```
