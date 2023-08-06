# 浏览器

## 内核

浏览器内核分成两部分：渲染引擎和JS脚本引擎，由于脚本引擎越来越独立，内核就倾向于只指渲染引擎

渲染引擎是一种对HTML文档进行解析并将其显示在页面上的工具，所有网页浏览器、电子邮件客户端、电子阅读器，以及其它需要根据标志性标记语言来显示内容的应用程序都需要渲染引擎

不同的浏览器内核对网页编写语法的解释有所不同，这也是网页编写者需要在不同内核的浏览器中测试网页显示效果的原因

- Trident（IE内核）：Edge浏览器（原名斯巴达，2015年新推出）现在也已经改为Chromium内核
- Gecko（Firefox内核）
- Presto（Opera旧内核）
- KDE 渲染引擎KHTML
- WebKit（Safari内核） Fork KHTML，渲染引擎WebCore
- Chromium（Chrome内核） Fork WebKit，渲染引擎：Blink（WebCore的分支，由Google和Opera Software开发），脚本引擎：V8，Node.JS其实就是封装了V8引擎
- Servo Mozilla与三星合作开发的下一代浏览器渲染引擎

## JS引擎

JavaScript之所以可以在浏览器中执行，是因为浏览器中的JavaScript解析引擎（也叫虚拟机）

JS 引擎能为程序员提供部分操作浏览器的功能（网络、DOM、外部事件、HTML5视频、canvas和存储）

- Mozilla Firefox: SpiderMonkey/TraceMonkey/JaegerMonkey/OdinMonkey
- Google Chrome: V8
- Apple Safari: Nitro/JSCore
- Internet Explorer: Chakra
- Microsoft Edge: V8

SpiderMonkey 是第一个 JS 引擎

Google Chrome 的 V8 引擎在2008年发布，被认为是 JS 引擎的一个重要里程碑。V8 引擎采用了一种叫做“即时编译”的技术，可以将 JavaScript 代码直接编译成机器码执行，从而大幅提高了 JavaScript 的执行速度。

## 交互函数

```js
// alert
alert("Hello");  // 弹出模态窗

// confirm()
let isTrue = confirm("true or false");  // 第一个模态窗，显示：内容、确定按钮、取消按钮
alert(`You select ${isTrue}`);  // 第二个模态窗：确定isTrue=true，取消isTrue=false

// prompt(title, [default])
let age = prompt('How old are you?', 100);  // 第一个模态窗，显示：标题、带默认值的输入框、确定按钮、取消按钮
alert(`You are ${age} years old!`); // 第一个模态窗：确定age=100，取消age=null
```

## Storage

- `Cookies` 最早的本地存储，由浏览器提供的功能，并对服务器和JS开放，所以我们可以通过服务器端或客户端保存Cookies。不过可以存储的数据总量大小只有 4KB
- `Local Storage` 持久化的本地存储，除非主动删除，否则会一直存储在本地
- `Session Storage` 只存在于Session会话中，只有在同一个Session的页面才能使用，当Session会话结束后，数据会自动释放掉。
- `WebSQL` 一种操作本地RDBMS（SQLite）的网页API接口
- `IndexedDB` 存储的是key-value类型（NoSQL）的数据，允许存储大量的数据，通常可以超过 250M，并且支持事务，当我们对数据进行增删改查（CRUD）的时候可以通过事务来进行。

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
