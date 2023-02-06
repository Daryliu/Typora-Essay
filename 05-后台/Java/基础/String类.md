#### String类

`String` 类是被 `final` 修饰的，因此 **「`String` 类不允许被继承」**

无论是 Java 8 还是 Java 9，**「用来存储数据的 char 或者 byte 数组 `value` 都一直是被声明为 `final` 的」**，这意味着 `value` 数组初始化之后就不能再引用其它数组了。并且 `String`内部没有改变 `value` 数组的方法，因此我们就说 `String` 是不可变的。

```Java
//检查一个字符串既不是 null也不为空串
if(str != null && str.length() != 0){
    // todo
}
```

```java 
String a = "hello";
String b = "world";
a = a + b; // a = "helloworld"
实际上，在使用 + 进行字符串拼接的时候，JVM 是初始化了一个 StringBuilder 来进行拼接的
```

我们还需要了解一个特性，当将一个字符串与一个非字符串的值进行拼接时，后者被自动转换成字符串（**「任何一个 Java 对象都可以转换成字符串」**）

- 对于基本数据类型来说， `==` 比较的是值是否相同；
- 对于引用数据类型来说， `==` 比较的是内存地址是否相同。

```Java
String str1 = new String("hello"); 
String str3 = str1.intern();
String str4 = str1.intern();
System.out.println(str3 == str4); // true
//可以使用 String的 intern()方法在运行过程中手动的将字符串添加到 String Pool 中
```

![图片](https://mmbiz.qpic.cn/mmbiz_png/PocakShgoGEEnCsDAd2sZFlZAHOSI15HOA2RsjIK5H4lbfZyq9DQFqjEgdtiahpPl38icu71Z9ZMictXE0xLPP68w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- `String str = "i"` 的方式，Java 虚拟机会自动将其分配到常量池中；
- `String str = new String(“i”)` 则会被分到堆内存中。可通过 intern 方法手动加入常量池



`StringBuilder` 不是线程安全的，在多线程环境下使用会出现数据不一致的问题，而 `StringBuffer` 是线程安全的。这是因为在 `StringBuffer` 类内，常用的方法都使用了`synchronized` 关键字进行同步，所以是线程安全的。而 `StringBuilder` 并没有。这也是运行速度 `StringBuilder` 大于 `StringBuffer` 的原因了。因此，如果在单线程下，优先考虑使用 `StringBuilder`。

`StringBuiler/StringBuffer`不能像 `String` 那样直接用字符串赋值，所以也不能那样初始化。它**「需要通过构造方法来初始化」**。首先， 构建一个空的字符串构建器：

```java
StringBuilder builder = new StringBuilder();

String mystr = builder.toString(); // 在需要构建字符串 String 时调用  toString 方法， 就能得到一个 String对象：
```

