### v-cli

###### 创建项目

```vue
在cmd中vue create xxxx
```

###### 查询

```vue
				  <tr v-for="hero in heros ">
                        <td>{{hero.name}}</td>
                        <td>{{hero.hp}}</td>
                    </tr>
```

###### 增加

```vue
		if(this.hero4Add.name.length==0)//如果名字为空
                    this.hero4Add.name = "Hero#"+this.hero4Add.id;
                //把对象加入到数组
                this.heros.push(this.hero4Add);
                //让 hero4Add 指向新的对象
                this.hero4Add = { id: 0, name: '', hp: '0'}
```

###### 删除

```vue
deleteHero: function (id) {		//根据id删除
                console.log("id"+id);
                for (var i=0;i<this.heros.length;i++){
                    if (this.heros[i].id == id) {
                        this.heros.splice(i, 1);
                        break;
                    }
                }
            }   
```

###### 修改

```vue
edit: function (hero) {
                $("#heroListTable").hide();
                $("#div4Update").show();
                this.hero4Update = hero;
            },
            update:function(){
                //因为v-model，已经同步修改了，所以只需要进行恢复显示就行了
                $("#heroListTable").show();
                $("#div4Update").hide();          
            },
            cancel:function(){
                //其实就是恢复显示
                $("#heroListTable").show();
                $("#div4Update").hide();
            }
```

