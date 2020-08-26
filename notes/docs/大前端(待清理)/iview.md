# View UI

## CDN 方式引入

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
