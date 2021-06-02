#### 组件

含义：每个产品展示，就是一个模板。 只用做一个模板，然后照着这个模板，传递不同的参数就可以看到不同的产品展示了。而此模板就是组件。

###### 局部组件

在 Vue对象里增加 components：

```vue
components:{
	'product':{ //组件名
		props:['name'],//传递参数
		//组件内容
		template:'<div class="product" >MAXFEEL休闲男士手包真皮手拿包大容量信封包手抓包夹包软韩版潮</div>'
	}
```

 然后在视图里，通过如下方式就可以<font color="yellowgreen">调用</font>了

```vue
<product></product>
```

###### 全局组件

```vue
<script>
Vue.component('product', {	//product为组件名
    props:['name'],//传递参数
    template: '<div class="product" >MAXFEEL休闲男士手包真皮手拿包大容量信封包手抓包夹包软韩版潮</div>'
    })
 
new Vue({
  el: '#div1'
})
</script>
```

**传递参数：通过传递参数来改变不同组件的内容**

```vue
<div id="div1">
    组件外的值：<input v-model="outName" ><br><!--outName改变则data中的对应改变 -->
    <product v-bind:name="outName"></product><!--data中的outname改变后会传递到此，再将此处的outname传递至组件中 -->
</div>
  
<script>
Vue.component('product', {
      props:['name'],  //将div中组件的outName传给name
      template: '<div class="product" >{{name}}</div>'
    })
 
new Vue({
  el: '#div1',
  data:{
      outName:'产品名称'
  }
})
</script>
```

###### 遍历JSON数组

**大部分时候是拿到一个 json 数组，然后遍历这个 json 数组为多个组件实例。**

```vue
<div id="div1">
    //在视图里通过 v-for 遍历 products    将遍历的item传递给product，方便下面调用
    <product v-for="item in products" v-bind:product="item"></product>  //将item传递给组件中的props:['product']
</div>
 
<script>
Vue.component('product', {
      props:['product'],
      template: '<div class="product" v-on:click="increaseSale">{{product.name}} 销量: {{product.sale}} </div>',
      methods:{
          increaseSale:function(){
              this.product.sale++
          }
      }
    })
 
new Vue({
  el: '#div1',
  data:{
      //产品数组
      products:[
                {"name":"MAXFEEL休闲男士手包真皮手拿包大容量信封包手抓包夹包软韩版潮","sale":"18"},
                {"name":"宾度 男士手包真皮大容量手拿包牛皮个性潮男包手抓包软皮信封包","sale":"35"},
                {"name":"唯美诺新款男士手包男包真皮大容量小羊皮手拿包信封包软皮夹包潮","sale":"29"}
                ]
  }
})
</script>
```

