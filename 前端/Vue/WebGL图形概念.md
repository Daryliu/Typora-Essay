### WebGL图形概念

##### 渲染

​		利用计算机程序从模型生成图像的过程；（如果要呈现的<u>图像比较复杂</u>,则<u>在专用服务器上进行渲染,</u>这个服务器需要有渲染复杂场景所需的足够硬件资源。它也被称为基于服务器的渲染.<u>CPU也可以在本地完成呈现</u>,它被称为基于客户端的渲染.）<font color="blue">WebGL遵循客户端的渲染方法</font>



#### Canvas

包含id、width、height三个属性

id：文档对象模型（dom）中的画布元素的标识符

##### 获取上下文（之后可调用所有的WebGL方法）

<u>第一步是让<font color="bluelight">WebGL渲染上下文 - Context 对象。这个对象与 WebGL 绘制缓冲区进行交互，可以调用所有WebGL的方法。</font></u>（为了得到上下文对象，需要调用当前 `HTMLCanvasElement` 的`getContext()`方法：）

`canvas.getContext(contextType, contextAttributes);`

```js
         var canvas = document.getElementById('my_Canvas');
         var gl = canvas.getContext('webgl');//获取上下文
```

contextAttributes非必要写，其属性如下：

| Alpha                 | 如果它的值为true,它将为画布提供一个alpha缓冲区。默认情况下为true. |
| --------------------- | ------------------------------------------------------------ |
| depth                 | 如果其值为true,您将得到一个绘图缓冲区,其中包含至少16位的深度缓冲区,默认情况下为true. |
| stencil               | 如果其值为true,您将得到一个绘图缓冲区,其中包含至少8位的模具缓冲区,默认情况下为false. |
| antialias             | 如果它的值是true，会得到一个绘图缓冲区，执行抗锯齿。默认情况下，它的值是true |
| premultipliedAlpha    | 如果它的值是true，会得到一个绘图缓冲区，其中包含的颜色与预乘alpha。默认情况下它的值是true |
| preserveDrawingBuffer | 如果它的值是true，缓冲区将不会被清零，直到被清除或由作者改写将保留它们的值。默认情况下，它的值是false |



#### WebGL着色器

WebGL不同于canvas 2D的绘图方式，<u>WebGL需要去调用OpenGL的接口的着色器去绘图</u>。

<font color="greenlight">顶点着色器:</font>用来描述顶点特性（如位置、颜色等）的程序(顶点:二三维空间中的一个点)

<font color="greenlight">片元着色器:</font>进行逐片元处理过程如光照（后续教程继续解释光照）的程序(片元:图像的单元)

```js
<script>
    //着色器必须预先处理成单个字符串的形式,所以要用+号将多行字符串连成一个长字符串,每行以\n结束
    
    //顶点着色器程序
    var VSHADER_SOURCE="" +
        "void main(){\n" +   //必须包含main函数且无返回值和参数
        "   gl_Position = vec4(0.0,0.0,0.0,1.0);\n" +//设置点的位置坐标(必须)
        "   gl_PointSize = 10.0;\n" +//设置点的尺寸
        "}\n";

    //片元着色器程序
    var FSHADER_SOURCE = "" +
        "void main(){\n" +
        "   gl_FragColor = vec4(1.0,0.0,0.0,1.0);\n" +//设置点的颜色
        "}\n";

    function main() {
        //首先获取到canvas的dom对象
        var canvas = document.getElementById("canvas");

        //获取到WebGL的上下文
        var gl = getWebGLContext(canvas);

        //不支持WebGL的浏览器将打印一个错误，并结束代码运行
        if (!gl) {
            console.log("浏览器不支持WebGL");
            return;
        }

        //初始化着色器
        if(!initShaders(gl,VSHADER_SOURCE,FSHADER_SOURCE)){//辅助函数initShaders,gl指定渲染的上下文,后二者为顶点和片元着色器,返回false和true
            console.log("初始化着色器失败");
            return;
        }

        //指定一个覆盖（清空）canvas的颜色
        gl.clearColor(0.0, 0.0, 0.0, 1.0);

        //执行清空
        gl.clear(gl.COLOR_BUFFER_BIT);

        //绘制一个点
        gl.drawArrays(gl.POINTS,0,1);
    }
</script>
```

