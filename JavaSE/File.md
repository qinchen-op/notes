***


>`java.io.File`类是文件和目录路径名的抽象表示，主要用于文件和目录的创建、查找和删除等操作。


## 构造方法

- `public File(String pathname)`：通过将给定的路径名字字符串转换为抽象路径名来创建性的`File`实例。
- `public File(String parent,String child)`：从父路径名字符串和子路径名字符串创建新的`File`实例。
- `public File(File parent,String child)`：从父抽象路径名和子路径名字符串创建新的`File`实例。

```java
//文件路径名
String pathname = "D:\\aaa.txt"
File file1 = new File(pathname)

// 文件路径名
String pathname2 = "D:\\aaa\\bbb.txt";
File file2 = new File(pathname2); 

// 通过父路径和子路径字符串
 String parent = "d:\\aaa";
 String child = "bbb.txt";
 File file3 = new File(parent, child);

// 通过父级File对象和子路径字符串
File parentDir = new File("d:\\aaa");
String child = "bbb.txt";
File file4 = new File(parentDir, child);

```


>1、一个`File`对象代表硬盘中实际存在的一个文件或者目录。
>2、无论该路径下是否存在文件或者目录，都不影响`File`对象的创建。



## 常用方法


#### 获取功能的方法

- `public String getAbsolutePath()`：返回`File`的绝对路径名字符串。
- `public String getPath()`：返回构造对象时的路径名。
- `public String geetName()`：返回文件或者目录名称。
- `public long length()`：返回此`File`对象所代表的文件的大小（字节码长度）。

方法演示：

```java
public class FileGet {
    public static void main(String[] args) {
        File f = new File("d:/aaa/bbb.java");     
        System.out.println("文件绝对路径:"+f.getAbsolutePath());
        System.out.println("文件构造路径:"+f.getPath());
        System.out.println("文件名称:"+f.getName());
        System.out.println("文件长度:"+f.length()+"字节");

        File f2 = new File("d:/aaa");     
        System.out.println("目录绝对路径:"+f2.getAbsolutePath());
        System.out.println("目录构造路径:"+f2.getPath());
        System.out.println("目录名称:"+f2.getName());
        System.out.println("目录长度:"+f2.length());
    }
}
输出结果：
文件绝对路径:d:\aaa\bbb.java
文件构造路径:d:\aaa\bbb.java
文件名称:bbb.java
文件长度:636字节

目录绝对路径:d:\aaa
目录构造路径:d:\aaa
目录名称:aaa
目录长度:4096
```

>`API`中说明：`length()`，表示文件的大小，但是当`File`对象表示目录时返回值未指定。


#### 判断功能的方法

- `public boolean exists()`：此`File`对象表示的文件或者目录是否实际存在。
- `public boolean isDirectory()`：是否是目录。
- `public boolean isFile()`：是否是文件。

演示如下：

```java
public class FileIs {
    public static void main(String[] args) {
        File f = new File("d:\\aaa\\bbb.java");
        File f2 = new File("d:\\aaa");
      	// 判断是否存在
        System.out.println("d:\\aaa\\bbb.java 是否存在:"+f.exists());
        System.out.println("d:\\aaa 是否存在:"+f2.exists());
      	// 判断是文件还是目录
        System.out.println("d:\\aaa 文件?:"+f2.isFile());
        System.out.println("d:\\aaa 目录?:"+f2.isDirectory());
    }
}
输出结果：
d:\aaa\bbb.java 是否存在:true
d:\aaa 是否存在:true
d:\aaa 文件?:false
d:\aaa 目录?:true
```


#### 创建删除功能的方法

- `public boolean createNewFile()`：当且仅当具有该名称的文件尚不存在时创建一个新的空位文件。
- `public boolean delete()`：删除文件或目录。
- `public boolean mkdir()`：创建目录。
- `public boolean mkdirs()`：创建目录，包括任何必须的但不存在的父目录。

演示代码：

```java
public class FileCreateDelete {
    public static void main(String[] args) throws IOException {
        // 文件的创建
        File f = new File("aaa.txt");
        System.out.println("是否存在:"+f.exists()); // false
        System.out.println("是否创建:"+f.createNewFile()); // true
        System.out.println("是否存在:"+f.exists()); // true
		
     	// 目录的创建
      	File f2= new File("newDir");	
        System.out.println("是否存在:"+f2.exists());// false
        System.out.println("是否创建:"+f2.mkdir());	// true
        System.out.println("是否存在:"+f2.exists());// true

		// 创建多级目录
      	File f3= new File("newDira\\newDirb");
        System.out.println(f3.mkdir());// false
        File f4= new File("newDira\\newDirb");
        System.out.println(f4.mkdirs());// true
      
      	// 文件的删除
       	System.out.println(f.delete());// true
      
      	// 目录的删除
        System.out.println(f2.delete());// true
        System.out.println(f4.delete());// false
    }
}
```

>`API`说明：`delete()`方法，如果对象表示目录，则目录必须为空才能删除。



#### 目录的遍历

- `public String[] list()`：用字符串数组表示该目录中的所有子文件或目录。
- `public File[] listFiles()`：返回一个`File`数组，表示该目录中所有的子文件或者目录。

代码演示：

```java
public class FileFor {  
    public static void main(String[] args) {  
        File dir = new File("d:\\java_code");  
        
        //获取当前目录下的文件以及文件夹的名称。  
        String[] names = dir.list();  
        for(String name : names){  
            System.out.println(name);  
        }  
        //获取当前目录下的文件以及文件夹对象，只要拿到了文件对象，那么就可以获取更多信息  
        File[] files = dir.listFiles();  
        for (File file : files) {  
            System.out.println(file);  
        }  
    }  
}
```

>注意：调用`listFiles`方法的`File`，表示的必须是实际存在的目录，否者返回`null`，无法进行遍历。

