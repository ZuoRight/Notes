# Vue

[官方文档](https://v3.cn.vuejs.org/guide/introduction.html){ .md-button .md-button--primary }

## 指令

v- 开头

### v-bind

简写：:xxx

### v-on:xxx

简写：@xxx

#### 绑定原生DOM事件

```text
@click: 鼠标单击时触发
@dblclick：鼠标双击时触发
@mouseenter：鼠标进入时触发
@mouseleave：鼠标移出时触发
```

#### 绑定自定义事件

点击按钮，如果想触发自定义事件，需要手动把自定义事件抛出来：$emit

```text
<todo-item @自定义的delete="handleDelete">
    <button @click="handleClick">点击删除</button>
<todo-item>

handleClick() {
    this.$emit("自定义的delete", 传递的参数this.xxx)
}

handleDelete(val) {
    console.log(val)  // val 可以拿到传参title
}
```

### v-modle

其实是一种语法糖，内部为：不同输入元素使用的属性并抛出不同事件，比如对于text输入框，v-modle就是:value="" @input="handleChange" 的简写形式

在表单元素上创建双向数据绑定，v-model会忽略所有表单元素的初始值，初始值需要在组件的data中声明。

```text
<input>
<textarea>
<select>
```

## 组件

全局注册

```text
语法：Vue.component('my-component-name', { /* ... */ })

Vue.component('组件名', {
    template: `pass`,  // 用反引号包裹，模板元素，如果有多个元素需要包含在一个父元素中
    props: pass,  // 声明参数
    data: function () {  // data的值必须是个函数
        pass
    },
    methods: {
        handleClick() {
            pass
        }
    }
})
```

## 插槽：v-slot:xxx

简写：#xxx

用于分发内容，一种传递复杂内容的方式

默认插槽：没有名称的插槽
具名插槽：有名称的插槽

```text
// 2.5版本这样标识
<span slot="插槽名称">xxx</span>

// 2.6版本这样标识
<template v-slot:插槽名称>
    <span>xxx</span>
</template>

// 引用插槽
<slot name="插槽名称"></slot>
```

作用域插槽：可以根据子组件传递不同的值返回不同的内容

```text
<template v-slot:插槽名称="值">
    <span>xxx</span>
</template>

// 引用插槽
<slot name="插槽名称" :value="value"></slot>
```

## iView

- CDN 方式引入

```html
<!-- import Vue.js -->
<script src="//vuejs.org/js/vue.min.js"></script>
<!-- import stylesheet -->
<link rel="stylesheet" href="//unpkg.com/view-design/dist/styles/iview.css">
<!-- import iView -->
<script src="//unpkg.com/view-design/dist/iview.min.js"></script>
```

注意事项：

1. 组件名要分隔，例如 DatePicker 必须要写成 date-picker

2. 以下组件，需要加前缀 i-：

    ```text
    Button: i-button
    Col: i-col
    Table: i-table
    Input: i-input
    Form: i-form
    Menu: i-menu
    Select: i-select
    Option: i-option
    Progress: i-progress
    Time: i-time
    ```

3. 以下组件，在所有模式下，必须加前缀 i-（除非使用 iview-loader）

    ```text
    Switch: i-switch
    Circle: i-circle
    ```

```html
<script type="text/javascript" src="{{ url_for('static', filename='login.js') }}"></script>
```
