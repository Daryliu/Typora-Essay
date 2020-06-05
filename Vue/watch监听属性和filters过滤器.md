#### watch监听属性

通过watch**来监听属性值的变化**。



#### 过滤器filters

**写法：{{ message | filterA |filterB }}**；<font color="greenyellow"><u>message是作为参数传递给filterA函数</u></font>，而函数filterA的返回值作为参数传递给函数filterB，最终显示是由filterB控制返回的

”在双括号中“：

```vue
{{ data | filter }}
```

”在v-bind表达式中”：

```vue
<div v-bind:data="data | filter">
    
</div>
```

案例：

```vue
				<!--使首字母为大写 -->
<div id="div1">
    <table align="center" >
        <tr class="firstLine">
            <td>输入数据</td>
            <td>过滤后的结果</td>
        </tr>      
        <tr>
            <td align="center">
                <input v-model= "data"  />
            </td>
            <td align="center">
                <!-- 过滤器设置的数据 -->
                {{ data|capitalize }}
            </td>
        </tr>
    </table>
   
</div>
      
<script>
    
new Vue({
      el: '#div1',
      data: {
          data:''
      },
      filters:{
          capitalize:function(value) {
                if (!value) return '' //如果为空，则返回空字符串
                value = value.toString()
                return value.charAt(0).toUpperCase() + value.substring(1)
          }
      }
    })
      
</script>
```

全局注册时是filter，无s；**组件过滤器是filters，有s**

###### 全局过滤器

通过全局过滤器的方式，只定义一次过滤器，然后就可以在不同的Vue对象里使用了

```vue
//capitalize为过滤器名称
Vue.filter('capitalize', function (value) {
	if (!value) return ''
	value = value.toString()
	return value.charAt(0).toUpperCase() + value.slice(1)
})
```

