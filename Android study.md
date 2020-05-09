# Android

1、bundle：用于不同activity之间数据传递

putString(String key, String value):<u>插入</u>一个给定key的字符串值（另一个activity里面取数据的时候，就要用到key，找出对应的value）

2、bundle与SharedPreferences的区别

SharedPreferences是简单的**存储持久化**的设置，就像用户每次打开应用程序时的主页，它只是一些简单的键值对来操作。它将数据保存在一个xml文件中

Bundle是将数据传递到另一个上下文中或保存或回复你自己状态的数据存储方式。它的数据不是持久化状态