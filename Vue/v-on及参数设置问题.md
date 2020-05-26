#### v-on 监听事件(@) 

在 js里为 Vue 对象的数据设置为 clickNumber

```vue
data: {
    clickNumber:0
}
```

新建一个方法： count， 其作用是 clickNumber 自增1

```vue
methods:{
    count: function(){
         this.clickNumber++;
    }
}
```

在按钮上增加 click 监听，调用 count 方法

```vue
<button v-on:click="count">点击</button>
```





参数传递：

```vue
//准备一个数组
var data = [
   		  {name:"盖伦",hp:341},
		  {name:"提莫",hp:225},
		  {name:"安妮",hp:427},
		  {name:"死歌",hp:893}
    ];
```

将数组作为参数传递至heros

```vue
 data:	{
    heros:data
}
```

