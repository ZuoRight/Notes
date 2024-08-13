# 浏览器

- <https://developer.chrome.com>
- [深入了解现代网络浏览器](https://developer.chrome.com/blog/inside-browser-part1)

浏览器是多进程的，通常每打开一个新标签页都会创建一个新的进程（有时多个空白标签会被合并为一个进程），其顶层存在一个 Browser Process 用以协调浏览器的其它进程

![20240412111552](https://image.zuoright.com/20240412111552.png)

- Browser Process 主进程，负责浏览器界面的显示与交互。各个页面的管理,创建和销毁其他进程。网络的资源管理、下载等
- Plugin Process 第三方插件进程，每种类型的插件对应一个进程，仅当使用该插件时才创建
- GPU Process GPU 进程，最多一个，用于3D绘制等
- Renderer Process: 渲染进程，内部是多线程的，每个标签页通常都有自己的渲染进程，用于页面渲染，脚本执行，事件处理等

![20240412120447](https://image.zuoright.com/20240412120447.png)

浏览器内核原本由渲染引擎和JS脚本引擎组成，但由于脚本引擎越来越独立，所以现在内核主要就是指渲染引擎

![20240412115916](https://image.zuoright.com/20240412115916.png)

在浏览器内核进程（渲染进程）中，GUI 渲染线程与 JS 引擎线程是互斥的

## 渲染引擎

渲染引擎是一种对HTML文档进行解析并将其显示在页面上的工具，所有网页浏览器、电子邮件客户端、电子阅读器，以及其它需要根据标志性标记语言来显示内容的应用程序都需要渲染引擎

不同的浏览器内核对网页编写语法的解释有所不同，这也是网页编写者需要在不同内核的浏览器中测试网页显示效果的原因

- Trident（IE 内核）：Edge 浏览器（原名斯巴达，2015年新推出）现在也已经改为 Chromium 内核
- Gecko（Firefox内核）
- Presto（Opera旧内核）
- KDE 渲染引擎KHTML
- WebKit（Safari内核） Fork KHTML，渲染引擎WebCore
- Chromium（Chrome内核） Fork WebKit
   - 渲染引擎：Blink（WebCore 的分支，由 Google 和 Opera Software 开发）
   - 脚本引擎：V8，Node.JS 其实就是封装了 V8 引擎
- Servo Mozilla 与三星合作开发的下一代浏览器渲染引擎

### 渲染流程

![20240412120157](https://image.zuoright.com/20240412120157.png)

## JS脚本引擎

JavaScript之所以可以在浏览器中执行，是因为浏览器中的JavaScript解析引擎（也叫虚拟机）

JS 引擎能为程序员提供部分操作浏览器的功能（网络、DOM、外部事件、HTML5视频、canvas和存储）

- Mozilla Firefox: SpiderMonkey/TraceMonkey/JaegerMonkey/OdinMonkey
- Google Chrome: V8
- Apple Safari: Nitro/JSCore
- Internet Explorer: Chakra
- Microsoft Edge: V8

SpiderMonkey 是第一个 JS 引擎

Google Chrome 的 V8 引擎在2008年发布，被认为是 JS 引擎的一个重要里程碑。V8 引擎采用了一种叫做“即时编译”的技术，可以将 JavaScript 代码直接编译成机器码执行，从而大幅提高了 JavaScript 的执行速度。

## Cache 缓存

缓存的作用是让网页加载更快，由您访问过的网页的文件、图像、视频、音频、脚本或其他多媒体文件组成。缓存始终存在于用户的计算机中，除非用户手动清除浏览器的缓存

```text
- Cache-Control
  - 缓存有效期
    - max-age=n1：有效期n秒，<=0立即失效
    - max-stale=n2：过期时间延长为n1+n2秒
    - min-fresh=n3：过期时间缩短为n1-n3秒
    - s-maxage=n：仅限制在代理服务器上的有效期
    - Expires

  - 如何使用缓存
    - no-store：不允许缓存
    - no-cache(页面强制刷新时触发)：需要先请求服务器验证是否失效
    - must-revalidate：缓存过期后再请求服务器验证是否失效
    - proxy-revalidate：代理服务器的缓存过期就必须回源服务器验证

  - 缓存类别(RFC没有明确规定默认类别)
    - public：缓存可以在代理服务器保存(专栏里说默认是这个)
    - private：缓存只能在客户端保存(百度百科说默认是这个)
  - no-transform：不允许代理对缓存数据做优化
  - only-if-cached：仅接受代理服务器上缓存的数据

- 验证缓存是否失效
  - 普通请求(页面后退/前进/重定向时触发)
    - 不会请求服务器，直接检查缓存(未失效显示：from disk cacahe)
  - 条件请求(页面刷新时触发)
    - if-Modified-Since
      - Last-modified：根据文件最后修改时间验证
    - If-None-Match
      - ETag
        - "根据语义级是否有修改验证"
        - W/"根据字节级是否有修改验证"

- X-Cache：标识代理服务器缓存是否命中
- X-Hit：标识代理服务器缓存的命中率
- Pragma:no-cache 与Cache- Control:no-cache相同
```

## Storage

- `Cookies` 最早的本地存储，由浏览器提供的功能，并对服务器和 JS 开放，所以我们可以通过服务器端或客户端保存 Cookies。不过可以存储的数据总量大小只有 4KB
- `Local Storage` 持久化的本地存储，除非主动删除，否则会一直存储在本地
- `Session Storage` 只存在于 Session 会话中，只有在同一个 Session 的页面才能使用，当 Session 会话结束后，数据会自动释放掉。
- `WebSQL` 一种操作本地 RDBMS（SQLite）的网页 API 接口
- `IndexedDB` 存储的是 key-value 类型（NoSQL）的数据，允许存储大量的数据，通常可以超过 250M，并且支持事务，当我们对数据进行增删改查的时候可以通过事务来进行。

Cookie 存储与用户网络浏览活动相关的信息，例如用户偏好、登录信息等。作用是跟踪用户的不同浏览活动

Local Storage 与 Session Storage 都属于 Web Storage。Web Storage 和 Cookies 类似，区别在于它有更大容量的存储。

WebSQL 与 IndexedDB 都是最新的 HTML5 本地缓存技术，相比于 Web Storage，存储功能更强大，支持的数据类型也更多，比如图片、视频等

```html
<!DOCTYPE HTML>
<html>
   <head>
      <meta charset="UTF-8">
      <title>WebSQL Demo</title> 
      <script type="text/javascript">
         // 初始化
         function init() {
            datatable = document.getElementById("datatable");
         }
         // 显示每个英雄的数据
         function showData(row){
            var tr = document.createElement("tr");
            var td1 = document.createElement("td");
            var td2 = document.createElement("td");
            var td3 = document.createElement("td");
            var td4 = document.createElement("td");
            var td5 = document.createElement("td"); 
            td1.innerHTML = row.id;
            td2.innerHTML = row.name;
            td3.innerHTML = row.hp_max;
            td4.innerHTML = row.mp_max;
            td5.innerHTML = row.role_main;
            tr.appendChild(td1);
            tr.appendChild(td2);
            tr.appendChild(td3);
            tr.appendChild(td4);
            tr.appendChild(td5);
            datatable.appendChild(tr);   
         }
         // 设置数据库信息，如果数据库已经存在，则直接打开，否则会创建
         // openDatabase(dbname, version, dbdesc, dbsize, callback_function() {});
         var db = openDatabase('wucai', '1.0', '王者荣耀英雄数据', 1024 * 1024);
         var msg;
           // 插入数据，事务操作：transaction()，执行SQL：executeSql()
         db.transaction(function (tx) {
            tx.executeSql('CREATE TABLE IF NOT EXISTS heros (id unique, name, hp_max, mp_max, role_main)');
            tx.executeSql('INSERT INTO heros (id, name, hp_max, mp_max, role_main) VALUES (10000, "夏侯惇", 7350, 1746, "坦克")');
            tx.executeSql('INSERT INTO heros (id, name, hp_max, mp_max, role_main) VALUES (10001, "钟无艳", 7000, 1760, "战士")');
            tx.executeSql('INSERT INTO heros (id, name, hp_max, mp_max, role_main) VALUES (10002, "张飞", 8341, 100, "坦克")');
            tx.executeSql('INSERT INTO heros (id, name, hp_max, mp_max, role_main) VALUES (10003, "牛魔", 8476, 1926, "坦克")');
            tx.executeSql('INSERT INTO heros (id, name, hp_max, mp_max, role_main) VALUES (10004, "吕布", 7344, 0, "战士")');
            msg = '<p>heros数据表创建成功，一共插入5条数据。</p>';
            document.querySelector('#status').innerHTML =  msg;
         });
         // 查询数据
         db.transaction(function (tx) {
            tx.executeSql('SELECT * FROM heros', [], function (tx, data) {
               var len = data.rows.length;
               msg = "<p>查询记录条数: " + len + "</p>";
               document.querySelector('#status').innerHTML +=  msg;
                  // 将查询的英雄数据放到 datatable中
               for (i = 0; i < len; i++){
                  showData(data.rows.item(i));
               }
            });

         });
      </script>
   </head> 
   <body>
      <div id="status" name="status">状态信息</div>
      <table border="1" id="datatable"></table>
   </body> 
</html>
```

![20220811131255](http://image.zuoright.com/20220811131255.png)

## Chrome 隐藏入口

```shell
about:version 显示当前版本
about:memory 显示本机浏览器内存使用状况
about:plugins 显示已安装插件
about:histograms 显示历史记录
about:dns 显示DNS状态
about:cache 显示缓存页面
about:gpu 是否有硬件加速
```

## Timing

<https://developer.chrome.com/docs/devtools/network/reference?utm_source=devtools&hl=zh-cn#timing>

![20240810121633](https://image.zuoright.com/20240810121633.png)
