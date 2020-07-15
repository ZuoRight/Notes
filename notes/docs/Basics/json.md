# JSON

> 参考：[廖雪峰JS教程：标准对象/JSON一节](https://www.liaoxuefeng.com/wiki/1022910821149312/1023021554858080)

全称：JavaScript Object Notation
用处：一种数据交换格式

在JSON出现之前，传递数据一直使用XML（一种纯文本格式），虽然它本身并不复杂，但它有一大堆规范，让人头大。但JSON出现后，成为了ECMA标准，所有的编程语言都有解析JSON的库，JS中可以直接使用，因为JS内置了JSON的解析。JSON实际就是JS的一个子集，其数据类型和JS几乎一致。

number：和JavaScript的number完全一致；
boolean：就是JavaScript的true或false；
string：就是JavaScript的string；
null：就是JavaScript的null；
array：就是JavaScript的Array表示方式——[]；
object：就是JavaScript的{ ... }表示方式。

JSON规定字符集必须是UTF-8，字符串必须用双引号。

要想在网络中传递数据，首先需要转化成JSON格式，即序列化。
然后再从JSON转换为相应语言的数据格式，即反序列化。

## 序列化

JSON.stringify(x);
//可以加一些参数控制输出，比如缩进形式输出
JSON.stringify(xiaoming, null, '  ');

## 反序列化

JSON.parse(json格式的数据);
