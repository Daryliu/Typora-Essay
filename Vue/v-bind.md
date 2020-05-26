#### v-bind

html属性不能使用双大括号形式绑定，只能使用v-bind指令;

**也就是说v-bind用于绑定html的元素属性。比如**

```vue
<div id="app">
    <p v-bind:title="title">html属性不能使用双大括号形式绑定，只能使用v-bind指令</p>
</div>
......
var vm = new Vue({
    el: '#app',
    data: {
        title: 'title content'
    }
});
```

**等价于**

```vue
<div id="app">
    <p title="title content">html属性不能使用双大括号形式绑定，只能使用v-bind指令</p>
</div>
```

将p元素中的title属性绑定