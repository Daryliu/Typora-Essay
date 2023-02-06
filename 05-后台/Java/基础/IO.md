### I/O

1>创建文件对象

```java
public static void main(String[] args) {
        // 创建文件对象
        File f1 = new File("d:/LOLFolder");
        System.out.println("f1的绝对路径：" + f1.getAbsolutePath());//getAbsolutePath获取“绝对路径”
        // 相对路径,相对于工作目录，如果在eclipse中，就是项目目录
        File f2 = new File("LOL.exe");
        System.out.println("f2的绝对路径：" + f2.getAbsolutePath());
  
        // 把f1作为父目录创建文件对象
        File f3 = new File(f1, "LOL.exe");
        System.out.println("f3的绝对路径：" + f3.getAbsolutePath());
    }
```

2> 2-1文件使用方法介绍

```java
public static void main(String[] args) {
  
        File f = new File("d:/LOLFolder/LOL.exe");
        System.out.println("当前文件是：" +f);
        //1   exists()文件是否存在
        System.out.println("判断是否存在："+f.exists());
         
        //2   isDirectory()是否是文件夹
        System.out.println("判断是否是文件夹："+f.isDirectory());
          
        //3   isFile()是否是文件（非文件夹）
        System.out.println("判断是否是文件："+f.isFile());
          
        //4   length()文件长度
        System.out.println("获取文件的长度："+f.length());
          
        //5   文件最后修改时间
        long time = f.lastModified();
        Date d = new Date(time);
        System.out.println("获取文件的最后修改时间："+d);
        //6   设置文件修改时间为1970.1.1 08:00:00
        f.setLastModified(0);
          
        //7   renameTo()文件重命名
        File f2 =new File("d:/LOLFolder/DOTA.exe");
        f.renameTo(f2);
}
```

   2-2文件使用方法介绍(续)

```java
public static void main(String[] args) throws IOException {
  
        File f = new File("d:/LOLFolder/skin/garen.ski");
  
        // 以字符串数组的形式，返回当前文件夹下的所有文件（不包含子文件及子文件夹）
        f.list();
  
        // 以文件数组的形式，返回当前文件夹下的所有文件（不包含子文件及子文件夹）
        File[]fs= f.listFiles();
  
        // 以字符串形式返回获取所在文件夹
        f.getParent();
  
        // 以文件形式返回获取所在文件夹
        f.getParentFile();
        // 创建文件夹，如果父文件夹skin不存在，创建就无效
        f.mkdir();
  
        // 创建文件夹，如果父文件夹skin不存在，就会创建父文件夹
        f.mkdirs();
  
        // 创建一个空文件,如果父文件夹skin不存在，就会抛出异常
        f.createNewFile();
        // 所以创建一个空文件之前，通常都会创建父目录
        f.getParentFile().mkdirs();
  
        // 列出所有的盘符c: d: e: 等等
        f.listRoots();
  
        // 刪除文件
        f.delete();
  
        // JVM结束的时候，刪除文件，常用于临时文件的删除
        f.deleteOnExit();
  
    }
```

#### Java的流Stream

1>流就是**一系列**的数据

2>当<u>不同的介质之间有数据交互</u>的时候，JAVA就使用流来实现。
	数据源可以是文件，还可以是数据库，网络甚至是其他的程序

##### 文件输入流

```java
			//目前代码只是建立了流，还没有开始读取
public static void main(String[] args) {
        try {
            File f = new File("d:/lol.txt");
            // 创建基于文件的输入流
            FileInputStream fis = new FileInputStream(f);
            // 通过这个输入流，就可以把数据从硬盘，读取到Java的虚拟机中来，也就是读取到内存中
            
            //FileOutputStream fos = new FileOutputStream(f);//创建基于文件的输出流
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

##### 字节流InputStream和OutputStream

InputStream是字节输入流，同时也是<u>抽象类，只提供方法声明，不提供方法的具体实现</u>。

[接上方代码：]

```java
			//以字节流的形式读取文件内容
public static void main(String[] args) {
        try {
            //准备文件lol.txt其中的内容是AB，对应的ASCII分别是65 66，接下来读取二者
            File f =new File("d:/lol.txt");
            //创建基于文件的输入流
            FileInputStream fis =new FileInputStream(f);
            //创建字节数组，其长度就是文件的长度
            byte[] all =new byte[(int) f.length()];
            
            ********************以字节流的形式读取文件所有内容*******************
            fis.read(all);
            for (byte b : all) {
                //打印出来是65 66
                System.out.println(b);
            }
             
           *************************每次使用完流，都应该进行关闭******************
            fis.close();
              
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
          
    }
```

```java
					//	以字节流的形式向文件写入数据
public static void main(String[] args) {
        try {
            // 准备文件lol2.txt其中的内容是空的
            File f = new File("d:/lol2.txt");
            // 准备长度是2的字节数组，用88,89初始化，其对应的字符分别是X,Y
            byte data[] = { 88, 89 };
 
            // 创建基于文件的输出流
            FileOutputStream fos = new FileOutputStream(f);
            // 把数据写入到输出流
            fos.write(data);
            // 关闭输出流
            ****************在try的作用域里关闭文件输入流，如果文件不存在，或者读取的时候出现问题而抛出异常，那么就不会执行这一行关闭流的代码，存在巨大的资源占用隐患*******************************
            fos.close();
             
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
 
    }
```

##### 关闭流

所有的流，无论是输入流还是输出流，使用完毕之后，都应该关闭。 如果不关闭，会产生对资源占用的浪费。

1>**在try的作用域里关闭文件输入流**，如果文件不存在，或者读取的时候出现问题而抛出异常，那么就不会执行这一行关闭流的代码，存在巨大的资源占用**隐患**

2>**正确关闭流**的方式：**在finally中关闭**

```java
public static void main(String[] args) {
        File f = new File("d:/lol.txt");
        FileInputStream fis = null;
        try {
            fis = new FileInputStream(f);
            byte[] all = new byte[(int) f.length()];
            fis.read(all);
            for (byte b : all) {
                System.out.println(b);
            }
 
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 在finally 里关闭流
            if (null != fis)
                try {
                    fis.close();
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
        }
    }
```

3>把流定义在try()里,try,catch或者finally结束的时候，**会自动关闭**

```java
public static void main(String[] args) {
        File f = new File("d:/lol.txt");
  
        //把流定义在try()里,try,catch或者finally结束的时候，会自动关闭
        try (FileInputStream fis = new FileInputStream(f)) {
            byte[] all = new byte[(int) f.length()];
            fis.read(all);
            for (byte b : all) {
                System.out.println(b);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
  
    }
```

##### 字符流Reader Writer

1、使用**字符流读取**文件：		Reader

```java
public static void main(String[] args) {
        // 准备文件lol.txt其中的内容是AB
        File f = new File("d:/lol.txt");
        // 创建基于文件的Reader
        try (FileReader fr = new FileReader(f)) {
            // 创建“字符数组”，其长度就是文件的长度
            char[] all = new char[(int) f.length()];
            // 以字符流的形式读取文件所有内容
            fr.read(all);
            for (char b : all) {
                // 打印出来是A B
                System.out.println(b);
            }
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
```

2、使用**字符流**把字符串**写入**到文件：		Writer

```java
public static void main(String[] args) {
        // 准备文件lol2.txt
        File f = new File("d:/lol2.txt");
        // 创建基于文件的Writer
        try (FileWriter fr = new FileWriter(f)) {
            // 以字符流的形式把数据写入到文件中
            String data="abcdefg1234567890";
            char[] cs = data.toCharArray();//将字符串转化为字符数组
            fr.write(cs);
  
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
  
    }
```

##### I/O中文编码UNICODE GBK UTF-8 ISO-8859-1 之间的区别

**ISO-8859-1 ASCII** 数字和西欧字母
**GBK GB2312简体中文 BIG5** 繁体中文；GBK同时包含简体和繁体以及日文
**UNICODE** (所有统一码，万国码)；UTF-8，UTF-16和UTF-32 针对不同类型的数据有不同的**减肥效果**，不至于空间浪费太多

