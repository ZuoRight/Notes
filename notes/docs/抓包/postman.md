# Postman

> 官方文档：<https://learning.postman.com/docs/getting-started/introduction/>
>
> API示例：<https://www.postman.com/explore>

![界面](https://assets.postman.com/postman-docs/navigating-postman-app-overview-v9.24.jpg)

## 发送请求

![20220831000738](http://image.zuoright.com/20220831000738.png)

### Params

### Body

- form-data

HTTP请求中的multipart/form-data，既可以上传文件等二进制数据，也可以上传表单键值对，只是最后会转化为一条信息

- x-www-form-urlencoded

只能上传键值对，并且键值对都是间隔分开的

- raw

可以上传多种文本格式：text、json、xml、html

- binary

只可以上传二进制数据，通常用来上传文件，一次只能上传一个文件

## 鉴权

<https://learning.postman.com/docs/sending-requests/authorization/>

## 抓包

默认使用代理方式，还可以使用拦截器方式（需要单独安装Chrome插件）

![20220902121108](http://image.zuoright.com/20220902121108.png)

抓取HTTPS协议接口，需要安装证书，在路径`~/Library/Application Support/Postman/proxy`找到证书文件双击后信任

![20220902120424](http://image.zuoright.com/20220902120424.png)

将浏览器或者设备代理到5555端口（我这里使用的是Chrome插件：SwitchyOmega）

![20220902121514](http://image.zuoright.com/20220902121514.png)

捕获数据，默认不补货返回信息，可以勾选，还可以筛选等

![20220902120551](http://image.zuoright.com/20220902120551.png)
