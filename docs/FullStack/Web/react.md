# React

- 官网：<https://zh-hans.react.dev/>
- 浏览器插件：[React Developer Tools](https://react.dev/learn/react-developer-tools)

用于构建用户界面的 JS 框架，只关注视图（不管发请求以及解析数据）

声明式以及组件化编码，可使用 RN(React Native) 编写原生应用

借助虚拟 DOM + Diffing 算法，减少真实 DOM 的操作，最小化页面重绘

## JSX

JSX = JS + XML

本质是 `React.creatElement(component, props, ...children)` 的语法糖

用于简化虚拟 DOM 的创建

```html
<body>
    <div id="test"></div>

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
</body>
```

```text
- 创建虚拟DOM时，不要写引号，多行标签可以放到 () 中
- 只能有一个根标签
- 标签必须闭合
- 标签中引入 JS 表达式，注意只能是表达式，要用 {...}，包括注释也要写在 {/* */} 中
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

## 组件

Component，用于实现局部功能的资源集合（HTML、CSS、JS、音视频等），提高复用性

组件可以使用类或函数定义，虽然使用类定义比较麻烦，但更推荐，因为函数定义有缺陷，需要使用 hooks 弥补，通常只用来定义简单的组件

- 简单组件
- 复杂组件（有状态组件）

### 函数式组件

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        //1. 使用函数定义组件，函数名首字母大写
        function Demo(){
            console.log(this) //此处的this是undefined，因为经过babel翻译，开启了严格模式
            return <h2>我是用函数定义的组件</h2>
        }
        //2. 组件挂载：渲染组件到页面
        ReactDOM.render(
            <Demo/>,  // 组件名首字母大写，标签要闭合，也可以写成 <Demo></Demo>
            document.getElementById('test')
        )
        /* 
        执行了ReactDOM.render(<Demo/>......后发生了什么？
            1. React解析组件标签，寻找Demo组件的定义位置
            2. React发现Demo组件是用函数定义的，随后React去直接调用Demo函数，将返回的虚拟DOM渲染到页面
        */
    </script>
</body>
```

### 类式组件

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        // 1. 使用类定义一个组件，类组件中的构造器完全可以省略
        class Demo extends React.Component{
            render(){
                console.log(this)
                return <h2>我是用类定义的组件</h2>
            }
        }
        // 2. 渲染组件到页面
        ReactDOM.render(<Demo/>,document.getElementById('test'))
        /* 
        执行了ReactDOM.render(<Demo/>......后发生了什么？
            1.React解析组件标签，寻找Demo组件的定义位置
            2.React发现Demo组件是用类定义的，React创建了一个Demo的实例对象————d
            3.通过d去调用到了render方法
        */
    </script>
</body>
```

## 组件核心属性

### state

存储数据，驱动组件页面的展示

- 基本使用

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Weather extends React.Component{
            constructor(props) {
                super(props)
                this.state = {isHot:true}  //初始化状态
                // 给实例自身追加一个同名的changeWeather方法，这个方法是根据原型上的changeWeather用bind生成的，解决changeWeather方法中获取不到this的问题
                this.changeWeather = this.changeWeather.bind(this)
            }

            render() {
                return <h1 onClick={this.changeWeather}>今天天气很{this.state.isHot ? '炎热' : '凉爽'}</h1>
            }

            // changeWeather 函数作为点击事件的回调函数，根本就不是通过Weather的实例调用的，由于类中的方法自动开启了严格模式，所以this是undefined
            changeWeather() {
                // this.state.isHot = true  // 无效
                // state 不可以直接被修改，要用setState去修改
                const isHot = this.state.isHot
                this.setState({isHot: !isHot})
            }
        }
        ReactDOM.render(<Weather/>,document.getElementById('test'))
    </script>
</body>
```

- 简写方式

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Weather extends React.Component{
            state = {isHot:true}  // 初始化状态
            render() {
                return <h1 onClick={this.changeWeather}>今天天气很{this.state.isHot ? '炎热' : '凉爽'}</h1>
            }
            // 组件类中程序员定义的事件回调，必须写成赋值语句+箭头函数，避免了this为undefined的问题
            changeWeather = ()=> {
                const isHot = this.state.isHot
                this.setState({isHot:!isHot})
            }
        }
        ReactDOM.render(<Weather/>,document.getElementById('test'))
    </script>
</body>
```

### props

类组件使用 props

```html
<head>
    ...
    <!-- 需要引入 prop-types，用于对标签属性进行限制 -->
    <script type="text/javascript" src="../js/prop-types.js"></script>
</head>

<body>
    <div id="test"></div>
    <div id="test2"></div>

    <script type="text/babel">
        class Person extends React.Component{
            // 对属性类型进行限制
            static propTypes = {
					name: PropTypes.string.isRequired,
					age: PropTypes.number,
					sex: PropTypes.string
				}
			// 给属性设置默认值
            static defaultProps = {
                age: 18
            }

            render() {
                // this.props.age = 99  // 直接改变属性值会报错，因为 props 对象中的属性都是只读的
                let {name, sex, age} = this.props  // 可以先赋值给变量，然后改变变量的值
                return (
                    <ul>
                        <li>姓名：{name}</li>
                        <li>性别：{sex}</li>
                        <li>年龄：{age + 1}</li>
                    </ul>
                )
            }
        }

        // 模拟一个服务器返回一个人的数据
        const p1 = {
            name: 'Alice',
            sex: '女',
            age: 19
        }
        // ReactDOM.render(<Person name="老刘" sex="女" age="66"/>, document.getElementById('test'))
        // 方式1：分别传递标签属性
        ReactDOM.render(<Person name={p1.name} sex={p1.sex} age={p1.age}/>, document.getElementById('test1'))
        // 方式2：批量传递标签属性，在JSX中，传递标签属性时可以使用 ... 直接展开对象
        ReactDOM.render(<Person {...p1}/>, document.getElementById('test2'))  
    </script>
</body>
```

函数式组件使用 props

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        // 函数没有 this，只能通过参数接收属性
        function Person(props){
            const {name, age, sex} = props
            return(
                <ul>
                    <li>姓名：{name}</li>
                    <li>性别：{sex}</li>
                    <li>年龄：{age}</li>
                </ul>
            )
        }

        // 对组件的限制只能加到外面
        Person.propTypes = {
            name:PropTypes.string.isRequired,
            age:PropTypes.number,
            sex:PropTypes.string
        }
        
        Person.defaultProps = {
            age:18
        }

        ReactDOM.render(<Person name="Bob" sex="男" age={19}/>, document.getElementById('test'))

    </script>
</body>
```

### refs

用于与真实 DOM 交互，比如管理焦点、触发强制动画、集成第三方 DOM 库等

有三种使用方法，官方推荐 createRef 形式的 ref

但由于 refs 会增加内存占用，官方建议能不用就不用

- 字符串形式

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Demo extends React.Component{
            render() {
                return (
                    <div>
                        <input type="text" ref="input1"/> &nbsp;
                        <button onClick={this.show}>点我提示左侧数据</button> &nbsp;
                        <input type="text" ref="input2" onBlur={this.show2} placeholder="失去焦点提示数据"/>
                    </div>
                )
            }
            show = ()=>{
                const {input1} = this.refs
                alert(input1.value)
            }

            show2 = ()=>{
                const {input2} = this.refs
                alert(input2.value)
            }
        }
        ReactDOM.render(<Demo/>,document.getElementById('test'))
    </script>
</body>
```

- 回调函数形式

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Demo extends React.Component{
            render() {
                return (
                    <div>
                        <input type="text" ref={c => this.input1 = c}/>&nbsp;
                        <button onClick={this.show}>点我提示左侧数据</button>&nbsp;
                        <input type="text" ref={c => this.input2 = c} onBlur={this.show2} placeholder="失去焦点提示数据"/>
                    </div>
                )
            }
            show = ()=>{
                const {input1} = this
                alert(input1.value)
            }

            show2 = ()=>{
                const {input2} = this
                alert(input2.value)
            }

        }
        ReactDOM.render(<Demo/>,document.getElementById('test'))
    </script>
</body>
```

- createRef 形式

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Demo extends React.Component{
            container = React.createRef()  // 容器1，用于存放节点
            container2 = React.createRef()  // 容器2，用于存放另一个节点
            render(){
                return (
                    <div>
                        {/* 把当前ref所在的节点，存入container容器 */}
                        <input type="text" ref={this.container} />&nbsp;
                        <button onClick={this.show}>点我提示左侧数据</button>&nbsp;
                        <input type="text" ref={this.container2} onBlur={this.show2} placeholder="失去焦点提示数据"/>
                    </div>
                )
            }
            show = ()=>{
                alert(this.container.current.value)
            }

            show2 = ()=>{
                alert(this.container2.current.value)
            }

        }
        ReactDOM.render(<Demo/>,document.getElementById('test'))
    </script>
</body>
```

## 事件处理

1. 通过 `onXxx` 属性指定事件处理函数（注意大小写）
    - 为了更好的兼容性，React 使用的是自定义事件（合成事件）
    - React 中的事件是通过事件委托方式处理的（委托给组件最外层的元素），效率更高
2. 通过 `event.target` 得到发生事件的 DOM 元素对象

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Demo extends React.Component{
            render(){
                return (
                    <div className="container">
                        <button onClick={this.show1}>按钮1</button>	
                        <button onClick={this.show2}>按钮2</button>
                        <div onClick={this.show3} className="child">xxxx</div>	
                    </div>
                )
            }
            show1 = (event)=>{
                // 接到的 event 是 React 自定义的事件对象，拥有着与原生 event 同样的属性
                event.stopPropagation()  // 停止冒泡
                console.log('按钮1', event)
            }
            show2 = (event)=>{
                console.log(event.target.innerText)
            }
            show3 = ()=>{
                console.log('xxxxx')
            }
        }

        ReactDOM.render(<Demo/>, document.getElementById('test'))
    </script>
</body>
```

## 收集表单数据

### 非受控组件，现用现取

表单中的数据，在提交的时候获取。通过 ref 获得到节点，进而访问到 value 值。

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Login extends React.Component{
            render(){
                return (
                    <form onSubmit={this.handleLogin}>
                        {/* 在 React 里 label 标签的 for 要写成 htmlFor，否则会与 React 本身的语法冲突 */}
                        <label htmlFor="username">用户名：</label>
                        <input id="username" type="text" ref={c => this.userNameNode = c}/><br/><br/>
                        密码：<input type="password" ref={c => this.passwordNode = c}/><br/><br/>
                        <button>登录</button>
                    </form>
                )
            }
            handleLogin = (event)=>{
                event.preventDefault()  // 组织表单默认行为-刷新
                const {userNameNode, passwordNode} = this
                alert(`用户名是${userNameNode.value}，密码是${passwordNode.value}`)
            }
        }

        ReactDOM.render(<Login/>,document.getElementById('test'))
    </script>
</body>
```

### 受控组件，收集数据

表单中输入类的 DOM，随着用户的输入，将值收集到 state 中

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Login extends React.Component{
            state = {
                username:'',
                password:''
            }
            
            render(){
                return (
                    <form onSubmit={this.handleLogin}>
                        用户名：<input type="text" onChange={this.saveUsername}/><br/><br/>
                        密码：<input type="password" onChange={this.savePassword}/><br/><br/>
                        <button>登录</button>
                    </form>
                )
            }

            // 保存用户名到 state 中
            saveUsername = (event)=>{
                this.setState({username:event.target.value})  // setState 不会造成无关数据的丢失
            }

            // 保存密码到 state 中
            savePassword = (event)=>{
                this.setState({password:event.target.value})
            }

            handleLogin = (event)=>{
                event.preventDefault()
                const {username,password} = this.state
                alert(`用户名是${username}，密码是${password}`)
            }
        }

        ReactDOM.render(<Login/>,document.getElementById('test'))
    </script>
</body>
```

### 高阶函数，收集数据

```text
高阶函数
    - 若A函数，接收的参数是一个函数，那么A就可以称之为高阶函数。比如：Promise、setTimeout、arr.map()
    - 若A函数，调用的返回值依然是一个函数，那么A就可以称之为高阶函数。比如：bind

函数的柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式
    function sum(a){
        return (b)=>{
            return (c)=>{
                return a+b+c
            }
        }
    }
```

```js
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Login extends React.Component{
            //初始化状态
            state = {
                username:'',
                password:''
            }
            
            render(){
                return (
                    <form onSubmit={this.handleLogin}>
                        用户名：<input type="text" onChange={this.saveFormData('username')}/><br/><br/>
                        密码：<input type="password" onChange={this.saveFormData('password')}/><br/><br/>
                        <button>登录</button>
                    </form>
                )
            }

            // 函数柯里化，闭包
            saveFormData = (type)=>{
                return (event)=> this.setState({[type]:event.target.value})
            }

            // 登录按钮的回调
            handleLogin = (event)=>{
                event.preventDefault()
                const {username,password} = this.state
                alert(`用户名是${username}，密码是${password}`)
            }
        }

        ReactDOM.render(<Login/>,document.getElementById('test'))
    </script>
</body>
```

### 非高阶函数，收集数据

```html
<body>
    <div id="test"></div>

    <script type="text/babel">
        class Login extends React.Component{
            //初始化状态
            state = {
                username:'',
                password:''
            }
            
            render(){
                return (
                    <form onSubmit={this.handleLogin}>
                        用户名：<input type="text" onChange={ event => this.saveFormData(event,'username') }/><br/><br/>
                        密码：<input type="password" onChange={ event => this.saveFormData(event,'password') }/><br/><br/>
                        <button>登录</button>
                    </form>
                )
            }

            saveFormData = (event, type)=>{
                this.setState({[type]:event.target.value})
            }

            //登录按钮的回调
            handleLogin = (event)=>{
                event.preventDefault()
                const {username,password} = this.state
                alert(`用户名是${username}，密码是${password}`)
            }
        }

        ReactDOM.render(<Login/>,document.getElementById('test'))
    </script>
</body>
```
