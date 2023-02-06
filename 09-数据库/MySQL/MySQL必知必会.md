##### Limit

检索出来的第一行为行0而不是行1。因此，**LIMIT 1, 1将检索出第二行而不是第一行**。（从第一行开始取一行，即获得第二行）

`LIMIT 4 OFFSET 3`意为从行3开始取4行，就像LIMIT 3, 4一样

##### 排序

DESC关键字只应用到直接位于其前面的列名。在上例中，只对prod_price列指定DESC，对prod_name列不指定。因此，prod_price列以降序排序，而prod_name列（在每个价格内）仍然按标准的升序排序。

**在多个列上降序排序 如果想在多个列上进行降序排序，必须对每个列指定DESC关键字。**

##### IN

IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。

```sql
select name,price from products where id IN (1,2,3) order by name;
此SELECT语句检索供应商1002和1003制造的所有产品。

SELECT * FROM "cycs_hxfx_water_corrosion" WHERE project_code IN ('HN_SJY03_GA268_201311','HN_SJY03_GA267_201310')
```

##### LIKE

###### `%`

匹配多个字符

![image-20220921164800451](C:\Users\hpp\AppData\Roaming\Typora\typora-user-images\image-20220921164800451.png)

###### `_`

只匹配单个字符而不是多个字符

![image-20220921164747635](C:\Users\hpp\AppData\Roaming\Typora\typora-user-images\image-20220921164747635.png)

##### 正则表达式

正则表达式的作用是匹配文本，将一个模式（正则表达式）与一个文本串进行比较。（包含即可，类似like的%%）

###### REGEXP

```sql
MYSQL：REGEXP
SELECT * FROM info_project WHERE PKIAA REGEXP 'HN_DDY01_GZ3'
BINARY：区分大小写
SELECT * FROM info_project WHERE PKIAA REGEXP BINARY 'HN_DDY01_GZ3'

POSTGRESQL：~
SELECT project_code FROM cycs_hxfx_water_corrosion WHERE project_code ~ 'HN_SJY03_GA268_201311'
```

###### 正则中的`or`

使用|（MySQL、PG同理）

```sql
SELECT * FROM info_project WHERE PKIAA REGEXP 'HN_DDY01_GZ3|HN_DDY01_GZ2'
```

###### 匹配几个字符之一

匹配单一字符。只想匹配特定的字符，可通过指定一组用[]括起来的字符来完成

**集合使用[1-9]这种写法**

```SQL
MYSQL
SELECT * FROM info_project WHERE PKIAA REGEXP '[123] ton'
```

<img src="C:\Users\hpp\AppData\Roaming\Typora\typora-user-images\image-20220921181150991.png" alt="image-20220921181150991" style="zoom:50%;" />

```sql
PG
SELECT project_code FROM cycs_hxfx_water_corrosion WHERE project_code ~ '[0-9] N_HGY01_GZ53'
```

###### 匹配特殊字符