# React

官网：<https://zh-hans.react.dev/>

用于构建用户界面的 JS 框架，只关注视图（不管发请求以及解析数据）

声明式以及组件（Component）化编码，可使用 RN(React Native) 编写原生应用

借助虚拟 DOM + Diffing 算法，减少真实 DOM 的操作，最小化页面重绘

## JSX

JSX = JS + XML

本质是 `React.creatElement(component, props, ...children)` 的语法糖

用于简化虚拟 DOM 的创建

```html
<script type="text/babel" >		
    const data = 'Hello, React'  // 模拟一些数据
    // 1. 创建虚拟 DOM
    const VDOM = (
        <div>
            <h1 style={{color:'white', fontSize:'40px'}} className="title"> {data} </h1>
            <h2> Hello, World </h2>
            <input type="text"/>
            {/* <Demo>组件</Demo> */}
        </div>
    )
    // 2. 将虚拟 DOM 渲染到页面
    ReactDOM.render(VDOM, document.getElementById('test'))
</script>
```

```text
- 创建虚拟DOM时，不要写引号，多行标签可以放到 () 中
- 只能有一个根标签
- 标签必须闭合
- 标签中引入 JS 表达式，注意只能是表达式，要是用 {...}，包括注释也要写在 {/* */} 中
- 标签中的内联样式要用 {{...}}，且注属性名转为小驼峰：style={{color:'white', fontSize:'30px'}}
- 标签中样式的类名要用 className 指定
- 标签首字母：小写为标签名，大写为组件
    - 若首字母小写，那么React就会去寻找与之同名的html标签
        若找见，直接转为html同名元素
        若未找见，报错
    - 若首字母大写，那么React就会去寻找与之同名的组件
        若找见，那么就是用组件
        若未找见，报错
```
