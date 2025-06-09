***


## 什么是`IO`?

>生活中，你肯定经历过这样的场景。当你编辑一个文本文件，忘记了`ctrl+s` ，可能文件就白白编辑了。当你电脑上插入一个U盘，可以把一个视频，拷贝到你的电脑硬盘里。那么数据都是在哪些设备上的呢？键盘、内存、硬盘、外接设备等等。

我们把这种数据的传输，可以看做是一种数据的流动，按照流动的方向，以内存为基准，分为`输入input` 和`输出output` ，即流向内存是输入流，流出内存的输出流。

Java中I/O操作主要是指使用`java.io`包下的内容，进行输入、输出操作。**输入**也叫做**读取**数据，**输出**也叫做作**写出**数据。


## `IO`的分类

根据数据的流向分为：*输入流*和*输出流*

- 输入流：其他设备 -> 内存

- 输出流：内存 -> 其他设备


根据数据的类型分为：*字节流*和*字符流*

- 字节流：以字节为单位读取数据

- 字符流：以字符为单位读取数据



## 顶级父类


|     | 输入流                | 输出流                 |
| --- | ------------------ | ------------------- |
| 字节流 | 字节输入流`InputStream` | 字节输出流`OutputStream` |
| 字符流 | 字符输入流`Reander`     | 字符输出流`Writer`       |


## 字节流

>一切文件数据（图片、文本、视频等）在存储时都是以二进制的形式保存的，上都是一个个字节，那么传输时也是以这样的形式。所以，字节流可以传输任意文件数据。在操作流的时候，我们要时刻明确，无论使用什么样的流对象，底层传输的始终都是二进制文件。


### 字节输出流（`OutputStream`)

`java.io.OutputStream`抽象类时表示字节输出流的所有类的超类，将指定的字节信息写到目的地。它定义了字节输出流的基本共性功能方法。

- `public void close()`：关闭此输出流并释放与此输出流相关联的任何系统资源。

- `public void flush()`：刷新此输出流并强制任何缓冲的输出字节被写出。

- `public void write(byte[] b)`：将`b.length`字节从指定的字节数组写入此输出流。

- `public void write(byte[] b, int off, int len)` ：从指定的字节数组写入 `len`字节，从偏移量 `off`开始输出到此输出流。

- `public abstract void write(int b)` ：将指定的字节输出流。


>`close`方法，当完成流的操作时必须调用此方法，释放系统资源。



#### `FileOutputStream`类

`java.io.FileOutputStream`类是文件输出流，用于将数据写出到文件。

*构造方法*

- `public FileOutputStream(File file)`：创建文件输出流以写入由指定的`File`对象表示的文件。

- `public FileOutputStream(String name)`：创建文件输出流以指定的名称写入文件。

>当你创建一个流对象时，必须传入一个文件路径。该路径下，如果没有这个文件，会创建该文件。如果有这个文件，会清空这个文件的数据。

代码如下：

```java
public class FileOutputStreamConstructor throws IOException {
    public static void main(String[] args) {
   	 	// 使用File对象创建流对象
        File file = new File("a.txt");
        FileOutputStream fos = new FileOutputStream(file);
      
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("b.txt");
    }
}
```


*写出字节数据*

1. 写出字节：`write(int b)`方法，每次可以写出一个字节数据，代码使用演示：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt");     
      	// 写出数据
      	fos.write(97); // 写出第1个字节
      	fos.write(98); // 写出第2个字节
      	fos.write(99); // 写出第3个字节
      	// 关闭资源
        fos.close();
    }
}
输出结果：
abc
```

>1. 虽然参数为int类型四个字节，但是只会保留一个字节的信息写出。
>2.流操作完毕后，必须释放系统资源，调用`close`方法，千万记得。


2. 写出字节数组：`write(byte[] b)`，每次可以写出数组中的数据，代码使用演示：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt");     
      	// 字符串转换为字节数组
      	byte[] b = "黑马程序员".getBytes();
      	// 写出字节数组数据
      	fos.write(b);
      	// 关闭资源
        fos.close();
    }
}
输出结果：
黑马程序员
```


3. 写出指定长度的字节数组：`write(byte[] b,int off,int len)`，每次写出从`off`索引开始，`len`个字节，代码演示如下：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt");     
      	// 字符串转换为字节数组
      	byte[] b = "abcde".getBytes();
		// 写出从索引2开始，2个字节。索引2是c，两个字节，也就是cd。
        fos.write(b,2,2);
      	// 关闭资源
        fos.close();
    }
}
输出结果：
cd
```


4. 数据最佳续写

>经过以上的演示，每次程序运行，创建输出流对象，都会清空目标文件中的数据。如何保留目标文件中数据，还能继续添加新数据呢？

- `public FileOutputStream(File file,boolean append)`：创建文件输出流对象是指定`append`为·`true`。

- `public FileOutputStream(String name,boolean append)`

这两个构造方法，参数中都需要传入一个boolean类型的值，`true` 表示追加数据，`false` 表示清空原有数据。这样创建的输出流对象，就可以指定是否追加续写了，代码使用演示：

```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt"，true);     
      	// 字符串转换为字节数组
      	byte[] b = "abcde".getBytes();
		// 写出从索引2开始，2个字节。索引2是c，两个字节，也就是cd。
        fos.write(b);
      	// 关闭资源
        fos.close();
    }
}
文件操作前：cd
文件操作后：cdabcde
```


5. 写出换行

`Windows`系统内，换行符号是`\r\n`。需要换行是把该符号的字节数组加入到后面就可以了。
```java
public class FOSWrite {
    public static void main(String[] args) throws IOException {
        // 使用文件名称创建流对象
        FileOutputStream fos = new FileOutputStream("fos.txt");  
      	// 定义字节数组
      	byte[] words = {97,98,99,100,101};
      	// 遍历数组
        for (int i = 0; i < words.length; i++) {
          	// 写出一个字节
            fos.write(words[i]);
          	// 写出一个换行, 换行符号转成数组写出
            fos.write("\r\n".getBytes());
        }
      	// 关闭资源
        fos.close();
    }
}

输出结果：
a
b
c
d
e
```

- 回车符`\r`和换行符`\n` ：
    
    - 回车符：回到一行的开头（return）。
        
    - 换行符：下一行（newline）。
        
- 系统中的换行：
    
    - Windows系统里，每行结尾是 `回车+换行` ，即`\r\n`；
        
    - Unix系统里，每行结尾只有 `换行` ，即`\n`；
        
    - Mac系统里，每行结尾是 `回车` ，即`\r`。从 Mac OS X开始与Linux统一。




### 字节输入流（`InputStream`）

`java.io.InputStream` 抽象类是表示字节输入流的所有类的超类，可以读取字节信息到内存中。它定义了字节输入流的基本共性功能方法。

- `public void close()` ：关闭此输入流并释放与此流相关联的任何系统资源。
    
- `public abstract int read()`： 从输入流读取数据的下一个字节。
    
- `public int read(byte[] b)`： 从输入流中读取一些字节数，并将它们存储到字节数组 b中 。
    

> 小贴士：
> 
> close方法，当完成流的操作时，必须调用此方法，释放系统资源。


#### `FileInputStream`类

`java.io.FileInputStream` 类是文件输入流，从文件中读取字节。

*构造方法*

- `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 `FileInputStream` ，该文件由文件系统中的 `File`对象 `file`命名。

- `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 `FileInputStream` ，该文件由文件系统中的路径名 `name`命名。

当你创建一个流对象时，必须传入一个文件路径。该路径下，如果没有文件会抛出`FileNotFoundException`

代码演示：

```java
public class FileInputStreamConstructor throws IOException{
    public static void main(String[] args) {
   	 	// 使用File对象创建流对象
        File file = new File("a.txt");
        FileInputStream fos = new FileInputStream(file);
      
        // 使用文件名称创建流对象
        FileInputStream fos = new FileInputStream("b.txt");
    }
}
```

*读取字节数据*

1. 读取字节：`read`方法，每次可以读取一个字节的数据，返回`int`类型，读取到最后会返回`-1`，代码演示如下：

```java
public class FISRead {
    public static void main(String[] args) throws IOException{
        // 使用文件名称创建流对象
        FileInputStream fis = new FileInputStream("read.txt");
        // 读取数据，返回一个字节
        int read = fis.read();
        System.out.println((char) read);
        read = fis.read();
        System.out.println((char) read);
        read = fis.read();
        System.out.println((char) read);
        read = fis.read();
        System.out.println((char) read);
        read = fis.read();
        System.out.println((char) read);
        // 读取到末尾,返回-1
        read = fis.read();
        System.out.println( read);
        // 关闭资源
        fis.close()
    }
}
输出结果：
a
b
c
d
e
-1
```

虚幻改进读取方式，代码如下：

```java
public class FISRead {
    public static void main(String[] args) throws IOException{
      	// 使用文件名称创建流对象
       	FileInputStream fis = new FileInputStream("read.txt");
      	// 定义变量，保存数据
        int b ；
        // 循环读取
        while ((b = fis.read())!=-1) {
            System.out.println((char)b);
        }
		// 关闭资源
        fis.close();
    }
}
输出结果：
a
b
c
d
e
```

>小贴士：
>1. 虽然读取了一个字节，但是会自动提升为`int`类型。  
>2. 流操作完毕后，必须释放系统资源，调用`close`方法，千万记得。


2. 使用字节数组读取：`read(byte[] b)`，每次读取b的长度个字节到数组中，返回读取到的有效字节个数，读取到末尾时，返回`-1` ，代码使用演示：

```java
public class FISRead {
    public static void main(String[] args) throws IOException{
      	// 使用文件名称创建流对象.
       	FileInputStream fis = new FileInputStream("read.txt"); // 文件中为abcde
      	// 定义变量，作为有效个数
        int len ；
        // 定义字节数组，作为装字节数据的容器   
        byte[] b = new byte[2];
        // 循环读取
        while (( len= fis.read(b))!=-1) {
           	// 每次读取后,把数组变成字符串打印
            System.out.println(new String(b));
        }
		// 关闭资源
        fis.close();
    }
}

输出结果：
ab
cd
ed
```

错误数据`d`，是由于最后一次读取时，只读取一个字节`e`，数组中，上次读取的数据没有被完全替换，所以要通过`len` ，获取有效的字节，代码使用演示：

```java
public class FISRead {
    public static void main(String[] args) throws IOException{
      	// 使用文件名称创建流对象.
       	FileInputStream fis = new FileInputStream("read.txt"); // 文件中为abcde
      	// 定义变量，作为有效个数
        int len ；
        // 定义字节数组，作为装字节数据的容器   
        byte[] b = new byte[2];
        // 循环读取
        while (( len= fis.read(b))!=-1) {
           	// 每次读取后,把数组的有效字节部分，变成字符串打印
            System.out.println(new String(b，0，len));//  len 每次读取的有效字节个数
        }
		// 关闭资源
        fis.close();
    }
}

输出结果：
ab
cd
e
```

>小贴士：
>
>使用数组读取，每次读取多个字节，减少了系统间的IO操作次数，从而提高了读写的效率，建议开发中使用。




## 字符流

>当使用字节流读取文本文件时，可能会有一个小问题。就是遇到中文字符时，可能不会显示完整的字符，那是因为一个中文字符可能占用多个字节存储。所以Java提供一些字符流类，以字符为单位读写数据，专门用于处理文本文件。



### 字符输入流(`Reader`)

`java.io.Reader`抽象类是表示用于读取字符流的所有类的超类，可以读取字符信息到内存中。它定义了字符输入流的基本共性功能方法。

- `public void close()` ：关闭此流并释放与此流相关联的任何系统资源。
    
- `public int read()`： 从输入流读取一个字符。
    
- `public int read(char[] cbuf)`： 从输入流中读取一些字符，并将它们存储到字符数组 cbuf中 。


#### `FileReader`

`java.io.FileReader` 类是读取字符文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

> 小贴士：
> 
> 1. 字符编码：字节与字符的对应规则。`Windows`系统的中文编码默认是`GBK`编码表。
>     
>     `idea`中`UTF-8`
>     
> 2. 字节缓冲区：一个字节数组，用来临时存储字节数据。
>     

*构造方法*

- `FileReader(File file)`： 创建一个新的 `FileReader` ，给定要读取的File对象。
    
- `FileReader(String fileName)`： 创建一个新的 `FileReader` ，给定要读取的文件的名称。
    

当你创建一个流对象时，必须传入一个文件路径。类似于`FileInputStream `。

演示代码如下：

```java
public class FileReaderConstructor throws IOException{
    public static void main(String[] args) {
   	 	// 使用File对象创建流对象
        File file = new File("a.txt");
        FileReader fr = new FileReader(file);
      
        // 使用文件名称创建流对象
        FileReader fr = new FileReader("b.txt");
    }
}
```


*读取字符数据*

1. **读取字符**：`read`方法，每次可以读取一个字符的数据，提升为int类型，读取到文件末尾，返回`-1`，循环读取，代码使用演示：
    

```java
public class FRRead {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileReader fr = new FileReader("read.txt");  
        // 定义变量，保存数据  
        int b ；  
        // 循环读取  
        while ((b = fr.read())!=-1) {  
            System.out.println((char)b);  
        }  
        // 关闭资源  
        fr.close();  
    }  
}  
输出结果：  
黑  
马  
程  
序  
员
```

> 小贴士：虽然读取了一个字符，但是会自动提升为int类型。

2. **使用字符数组读取**：`read(char[] cbuf)`，每次读取b的长度个字符到数组中，返回读取到的有效字符个数，读取到末尾时，返回`-1` ，代码使用演示：
    

```java
public class FRRead {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileReader fr = new FileReader("read.txt");  
        // 定义变量，保存有效字符个数  
        int len ；  
        // 定义字符数组，作为装字符数据的容器  
         char[] cbuf = new char[2];  
        // 循环读取  
        while ((len = fr.read(cbuf))!=-1) {  
            System.out.println(new String(cbuf));  
        }  
        // 关闭资源  
        fr.close();  
    }  
}  
输出结果：  
黑马  
程序  
员序
```
获取有效的字符改进，代码使用演示：

```java
public class FISRead {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileReader fr = new FileReader("read.txt");  
        // 定义变量，保存有效字符个数  
        int len ；  
        // 定义字符数组，作为装字符数据的容器  
        char[] cbuf = new char[2];  
        // 循环读取  
        while ((len = fr.read(cbuf))!=-1) {  
            System.out.println(new String(cbuf,0,len));  
        }  
        // 关闭资源  
        fr.close();  
    }  
}  
​  
输出结果：  
黑马  
程序  
员
```


### 字符输出流（`Writer`）

`java.io.Writer` 抽象类是表示用于写出字符流的所有类的超类，将指定的字符信息写出到目的地。它定义了字节输出流的基本共性功能方法。

- `void write(int c)` 写入单个字符。
    
- `void write(char[] cbuf)` 写入字符数组。
    
- `abstract void write(char[] cbuf, int off, int len)` 写入字符数组的某一部分,off数组的开始索引,len写的字符个数。
    
- `void write(String str)` 写入字符串。
    
- `void write(String str, int off, int len)` 写入字符串的某一部分,off字符串的开始索引,len写的字符个数。
    
- `void flush()` 刷新该流的缓冲。
    
- `void close()` 关闭此流，但要先刷新它。

#### `FileWriter`

`java.io.FileWriter` 类是写出字符到文件的便利类。构造时使用系统默认的字符编码和默认字节缓冲区。

*构造方法*

- `FileWriter(File file)`： 创建一个新的 `FileWriter`，给定要读取的`File`对象。
    
- `FileWriter(String fileName)`： 创建一个新的 `FileWriter`，给定要读取的文件的名称。

代码演示如下

```java
public class FileWriterConstructor {
    public static void main(String[] args) throws IOException {
   	 	// 使用File对象创建流对象
        File file = new File("a.txt");
        FileWriter fw = new FileWriter(file);
      
        // 使用文件名称创建流对象
        FileWriter fw = new FileWriter("b.txt");
    }
}
```

*写出基本数据*

**写出字符**：`write(int b)` 方法，每次可以写出一个字符数据，代码使用演示：

```java
public class FWWrite {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileWriter fw = new FileWriter("fw.txt");       
        // 写出数据  
        fw.write(97); // 写出第1个字符  
        fw.write('b'); // 写出第2个字符  
        fw.write('C'); // 写出第3个字符  
        fw.write(30000); // 写出第4个字符，中文编码表中30000对应一个汉字。  
        
        /*  
        【注意】关闭资源时,与FileOutputStream不同。  
         如果不关闭,数据只是保存到缓冲区，并未保存到文件。  
        */  
        // fw.close();  
    }  
}  
输出结果：  
abC田
```


> 小贴士：
> 
> 1. 虽然参数为int类型四个字节，但是只会保留一个字符的信息写出。
>     
> 2. 未调用close方法，数据只是保存到了缓冲区，并未写出到文件中。


*关闭和刷新*

因为内置缓冲区的原因，如果不关闭输出流，无法写出字符到文件中。但是关闭的流对象，是无法继续写出数据的。如果我们既想写出数据，又想继续使用流，就需要`flush` 方法了。

- `flush` ：刷新缓冲区，流对象可以继续使用。
    
- `close` :先刷新缓冲区，然后通知系统释放资源。流对象不可以再被使用了。
    

代码使用演示：
```java
public class FWWrite {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileWriter fw = new FileWriter("fw.txt");  
        // 写出数据，通过flush  
        fw.write('刷'); // 写出第1个字符  
        fw.flush();  
        fw.write('新'); // 继续写出第2个字符，写出成功  
        fw.flush();  
        
        // 写出数据，通过close  
        fw.write('关'); // 写出第1个字符  
        fw.close();  
        fw.write('闭'); // 继续写出第2个字符,【报错】java.io.IOException: Stream closed  
        fw.close();  
    }  
}
```


> 小贴士：即便是flush方法写出了数据，操作的最后还是要调用close方法，释放系统资源。


*写出其他数据*

1. **写出字符数组** ：`write(char[] cbuf)` 和 `write(char[] cbuf, int off, int len)` ，每次可以写出字符数组中的数据，用法类似FileOutputStream，代码使用演示：
    
```java
public class FWWrite {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileWriter fw = new FileWriter("fw.txt");       
        // 字符串转换为字节数组  
        char[] chars = "黑马程序员".toCharArray();  
        
        // 写出字符数组  
        fw.write(chars); // 黑马程序员  
          
        // 写出从索引2开始，2个字节。索引2是'程'，两个字节，也就是'程序'。  
        fw.write(b,2,2); // 程序  
        
        // 关闭资源  
        fos.close();  
    }  
}
```

2. **写出字符串**：`write(String str)` 和 `write(String str, int off, int len)` ，每次可以写出字符串中的数据，更为方便，代码使用演示：
    
```java
public class FWWrite {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象  
        FileWriter fw = new FileWriter("fw.txt");       
        // 字符串  
        String msg = "黑马程序员";  
        
        // 写出字符数组  
        fw.write(msg); //黑马程序员  
        
        // 写出从索引2开始，2个字节。索引2是'程'，两个字节，也就是'程序'。  
        fw.write(msg,2,2);  // 程序  
          
        // 关闭资源  
        fos.close();  
    }  
}
```


3. **续写和换行**：操作类似于FileOutputStream。
    

```java
public class FWWrite {  
    public static void main(String[] args) throws IOException {  
        // 使用文件名称创建流对象，可以续写数据  
        FileWriter fw = new FileWriter("fw.txt"，true);       
        // 写出字符串  
        fw.write("黑马");  
        // 写出换行  
        fw.write("\r\n");  
        // 写出字符串  
        fw.write("程序员");  
        // 关闭资源  
        fw.close();  
    }  
}  
输出结果:  
黑马  
程序员
```


> 小贴士：字符流，只能操作文本文件，不能操作图片，视频等非文本文件。
> 
> 当我们单纯读或者写文本文件时 使用字符流 其他情况使用字节流



## `IO`异常处理

#### JDK7前处理

之前的入门练习，我们一直把异常抛出，而实际开发中并不能这样处理，建议使用`try...catch...finally` 代码块，处理异常部分，代码使用演示：

```java
public class HandleException1 {  
    public static void main(String[] args) {  
        // 声明变量  
        FileWriter fw = null;  
        try {  
            //创建流对象  
            fw = new FileWriter("fw.txt");  
            // 写出数据  
            fw.write("黑马程序员"); //黑马程序员  
        } catch (IOException e) {  
            e.printStackTrace();  
        } finally {  
            try {  
                if (fw != null) {  
                    fw.close();  
                }  
            } catch (IOException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
}
```


#### JDK7的处理(扩展知识点了解内容)

还可以使用JDK7优化后的`try-with-resource` 语句，该语句确保了每个资源在语句结束时关闭。所谓的资源（resource）是指在程序完成后，必须关闭的对象。

格式：

```java
try (创建流对象语句，如果多个,使用';'隔开) {  
    // 读写数据  
} catch (IOException e) {  
    e.printStackTrace();  
}
```


代码使用演示：

```java
public class HandleException2 {  
    public static void main(String[] args) {  
        // 创建流对象  
        try ( FileWriter fw = new FileWriter("fw.txt"); ) {  
            // 写出数据  
            fw.write("黑马程序员"); //黑马程序员  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
}
```


#### JDK9的改进(扩展知识点了解内容)

JDK9中`try-with-resource` 的改进，对于**引入对象**的方式，支持的更加简洁。被引入的对象，同样可以自动关闭，无需手动close，我们来了解一下格式。

改进前格式：

```java
// 被final修饰的对象  
final Resource resource1 = new Resource("resource1");  
// 普通对象  
Resource resource2 = new Resource("resource2");  
// 引入方式：创建新的变量保存  
try (Resource r1 = resource1;  
     Resource r2 = resource2) {  
     // 使用对象  
}
```

改进后格式：

```java
// 被final修饰的对象  
final Resource resource1 = new Resource("resource1");  
// 普通对象  
Resource resource2 = new Resource("resource2");  
​  
// 引入方式：直接引入  
try (resource1; resource2) {  
     // 使用对象  
}
```

改进后，代码使用演示：

```java
public class TryDemo {  
    public static void main(String[] args) throws IOException {  
        // 创建流对象  
        final  FileReader fr  = new FileReader("in.txt");  
        FileWriter fw = new FileWriter("out.txt");  
        // 引入到try中  
        try (fr; fw) {  
            // 定义变量  
            int b;  
            // 读取数据  
            while ((b = fr.read())!=-1) {  
                // 写出数据  
                fw.write(b);  
            }  
        } catch (IOException e) {  
            e.printStackTrace();  
        }  
    }  
}
```




## 缓冲流

缓冲流，也叫高效流，是对4个基本的`FileXxx`流的增强，所以也是4个流，按照数据类型分类：

- *字节缓冲流*：`BufferedInputStream`、`BufferedOutputStream`

- *字符缓冲流*：`BufferedReader`、`BufferedWriter`

缓冲流的基本原理是在创造流对象时会创造一个内置的默认大小的缓冲区数组，通过缓冲区读取，减少系统`IO`次数，从而提高读写的效率。


### 字节缓冲流

*构造方法*

- `public BufferedInputStream(InputStream in)`：创建一个新的缓冲输入流

- `public BufferedOutputStream(OutputStream out)`：创建一个新的缓冲输出流

代码演示：

```java
// 创建字节缓冲输入流
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("bis.txt"));
// 创建字节缓冲输出流
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("bos.txt"));
```


缓冲流的读写方法与基本的流是一致的。



### 字符缓冲流

*构造方法*

- `public BufferedReader(Reader in)`

- `public BufferedWriter(Writer out)`

代码演示

```java
// 创建字符缓冲输入流
BufferedReader br = new BufferedReader(new FileReader("br.txt"));
// 创建字符缓冲输出流
BufferedWriter bw = new BufferedWriter(new FileWriter("bw.txt"));
```


*特有方法*

字符缓冲流的基本方法与普通字符流调用方式一致，特有方法有：

- `BufferedReader`：`public String readLine()`：读取一行文字

- `BufferedWriter`：`public void newLine()`：写一行就分隔一行

`readLine`方法演示

```java
public class BufferedReaderDemo {
    public static void main(String[] args) throws IOException {
      	 // 创建流对象
        BufferedReader br = new BufferedReader(new FileReader("in.txt"));
		// 定义字符串,保存读取的一行文字
        String line  = null;
      	// 循环读取,读取到最后返回null
        while ((line = br.readLine())!=null) {
            System.out.print(line);
            System.out.println("------");
        }
		// 释放资源
        br.close();
    }
}
```

`newLine`方法演示

```java
public class BufferedWriterDemo throws IOException {
    public static void main(String[] args) throws IOException  {
      	// 创建流对象
		BufferedWriter bw = new BufferedWriter(new FileWriter("out.txt"));
      	// 写出数据
        bw.write("黑马");
      	// 写出换行
        bw.newLine();
        bw.write("程序");
        bw.newLine();
        bw.write("员");
        bw.newLine();
		// 释放资源
        bw.close();
    }
}
输出效果:
黑马
程序
员
```





## 转换流



### 字符编码和字符集

*字符编码*

>计算机中储存的信息都是用二进制数表示的，而我们在屏幕上看到的数字、英文、标点符号、汉字等字符是二进制数转换之后的结果。按照某种规则，将字符存储到计算机中，称为**编码** 。反之，将存储在计算机中的二进制数按照某种规则解析显示出来，称为**解码** 。比如说，按照A规则存储，同样按照A规则解析，那么就能显示正确的文本符号。反之，按照A规则存储，再按照B规则解析，就会导致乱码现象。


编码：字符 -> 字节

解码：字节 -> 字符

- **字符编码**`Character Encoding`：就是一套自然语言的字符与二进制数之间的对应规则。


*字符集*

- **字符集 `Charset`**：也叫编码表。是一个系统支持的所有字符的集合，包括各国家文字、标点符号、图形符号、数字等。

>计算机要准确的存储和识别各种字符集符号，需要进行字符编码，一套字符集必然至少有一套字符编码。常见字符集有ASCII字符集、GBK字符集、Unicode字符集等。


### 编码引出的问题

在IDEA中，使用`FileReader` 读取项目中的文本文件。由于IDEA的设置，都是默认的`UTF-8`编码，所以没有任何问题。但是，当读取Windows系统中创建的文本文件时，由于Windows系统的默认是GBK编码，就会出现乱码。

```java
public class ReaderDemo {  
    public static void main(String[] args) throws IOException {  
        FileReader fileReader = new FileReader("E:\\File_GBK.txt");  
        int read;  
        while ((read = fileReader.read()) != -1) {  
            System.out.print((char)read);  
        }  
        fileReader.close();  
    }  
}  
输出结果：  
���
```


那么如何读取GBK编码的文件呢？



### `InputStreamReader`

>转换流`java.io.InputStreamReader`，是`Reader`的子类，是从字节流到字符流的桥梁。它读取字节，并使用指定的字符集将其解码为字符。它的字符集可以由名称指定，也可以接受平台的默认字符集。


*构造方法*

- `InputStreamReader(InputStream in)`: 创建一个使用默认字符集的字符流。
    
- `InputStreamReader(InputStream in, String charsetName)`: 创建一个指定字符集的字符流。

代码演示

```java
InputStreamReader isr = new InputStreamReader(new FileInputStream("in.txt"));
InputStreamReader isr2 = new InputStreamReader(new FileInputStream("in.txt") , "GBK");
```


指定编码读取

```java
public class ReaderDemo2 {
    public static void main(String[] args) throws IOException {
      	// 定义文件路径,文件为gbk编码
        String FileName = "E:\\file_gbk.txt";
      	// 创建流对象,默认UTF8编码
        InputStreamReader isr = new InputStreamReader(new FileInputStream(FileName));
      	// 创建流对象,指定GBK编码
        InputStreamReader isr2 = new InputStreamReader(new FileInputStream(FileName) , "GBK");
		// 定义变量,保存字符
        int read;
      	// 使用默认编码字符流读取,乱码
        while ((read = isr.read()) != -1) {
            System.out.print((char)read); // ��Һ�
        }
        isr.close();
      
      	// 使用指定编码字符流读取,正常解析
        while ((read = isr2.read()) != -1) {
            System.out.print((char)read);// 大家好
        }
        isr2.close();
    }
}
```



### `OutputStreamWriter`

>转换流`java.io.OutputStreamWriter` ，是Writer的子类，是从字符流到字节流的桥梁。使用指定的字符集将字符编码为字节。它的字符集可以由名称指定，也可以接受平台的默认字符集。


*构造方法*

- `OutputStreamWriter(OutputStream in)`: 创建一个使用默认字符集的字符流。
    
- `OutputStreamWriter(OutputStream in, String charsetName)`: 创建一个指定字符集的字符流。

代码举例

```java
OutputStreamWriter isr = new OutputStreamWriter(new FileOutputStream("out.txt"));
OutputStreamWriter isr2 = new OutputStreamWriter(new FileOutputStream("out.txt") , "GBK");
```


指定编码写出

```java
public class OutputDemo {
    public static void main(String[] args) throws IOException {
      	// 定义文件路径
        String FileName = "E:\\out.txt";
      	// 创建流对象,默认UTF8编码
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream(FileName));
        // 写出数据
      	osw.write("你好"); // 保存为6个字节
        osw.close();
      	
		// 定义文件路径
		String FileName2 = "E:\\out2.txt";
     	// 创建流对象,指定GBK编码
        OutputStreamWriter osw2 = new OutputStreamWriter(new FileOutputStream(FileName2),"GBK");
        // 写出数据
      	osw2.write("你好");// 保存为4个字节
        osw2.close();
    }
}
```




## 序列化


### 概述

>Java 提供了一种对象**序列化**的机制。用一个字节序列可以表示一个对象，该字节序列包含该`对象的数据`、`对象的类型`和`对象中存储的属性`等信息。字节序列写出到文件之后，相当于文件中**持久保存**了一个对象的信息。
>
>反之，该字节序列还可以从文件中读取回来，重构对象，对它进行**反序列化**。`对象的数据`、`对象的类型`和`对象中存储的数据`信息，都可以用来在内存中创建对象。



### `ObjectOutputStream`


`java.io.ObjectOutputStream`类，将`Java`对象的原始数据类型写出到文件,实现对象的持久存储。

*构造方法*

- `public ObjectOutputStream(OutputStream out)` ： 创建一个指定`OutputStream`的`ObjectOutputStream`。

代码举例

```java
FileOutputStream fileOut = new FileOutputStream("employee.txt");
ObjectOutputStream out = new ObjectOutputStream(fileOut);
```


*序列化操作*

1. 一个对象要想序列化，必须满足两个条件:
    

- 该类必须实现`java.io.Serializable` 接口，`Serializable` 是一个标记接口，不实现此接口的类将不会使任何状态序列化或反序列化，会抛出`NotSerializableException` 。
    
- 该类的所有属性必须是可序列化的。如果有一个属性不需要可序列化的，则该属性必须注明是瞬态的，使用`transient` 关键字修饰。
    

```java
public class Employee implements java.io.Serializable {  
    public String name;  
    public String address;  
    public transient int age; // transient瞬态修饰成员,不会被序列化  
    public void addressCheck() {  
        System.out.println("Address  check : " + name + " -- " + address);  
    }  
}
```


2.写出对象方法

- `public final void writeObject (Object obj)` : 将指定的对象写出。
    

```java
public class SerializeDemo{  
    public static void main(String [] args)   {  
        Employee e = new Employee();  
        e.name = "zhangsan";  
        e.address = "beiqinglu";  
        e.age = 20;   
        try {  
            // 创建序列化流对象  
          ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("employee.txt"));  
            // 写出对象  
            out.writeObject(e);  
            // 释放资源  
            out.close();  
            fileOut.close();  
            System.out.println("Serialized data is saved"); // 姓名，地址被序列化，年龄没有被序列化。  
        } catch(IOException i)   {  
            i.printStackTrace();  
        }  
    }  
}  
输出结果：  
Serialized data is saved
```


### `ObjectInputStream`

`ObjectInputStream`反序列化流，将之前使用`ObjectOutputStream`序列化的原始数据恢复为对象。

*构造方法*

- `public ObjectInputStream(InputStream in)` ： 创建一个指定InputStream的ObjectInputStream。
    

*反序列化操作1*

如果能找到一个对象的class文件，我们可以进行反序列化操作，调用`ObjectInputStream`读取对象的方法：

- `public final Object readObject ()` : 读取一个对象。
    

```java
public class DeserializeDemo {  
   public static void main(String [] args)   {  
        Employee e = null;  
        try {         
             // 创建反序列化流  
             FileInputStream fileIn = new FileInputStream("employee.txt");  
             ObjectInputStream in = new ObjectInputStream(fileIn);  
             // 读取一个对象  
             e = (Employee) in.readObject();  
             // 释放资源  
             in.close();  
             fileIn.close();  
        }catch(IOException i) {  
             // 捕获其他异常  
             i.printStackTrace();  
             return;  
        }catch(ClassNotFoundException c)  {  
            // 捕获类找不到异常  
             System.out.println("Employee class not found");  
             c.printStackTrace();  
             return;  
        }  
        // 无异常,直接打印输出  
        System.out.println("Name: " + e.name);  // zhangsan  
        System.out.println("Address: " + e.address); // beiqinglu  
        System.out.println("age: " + e.age); // 0  
    }  
}
```


>对于JVM可以反序列化对象，它必须是能够找到class文件的类。如果找不到该类的class文件，则抛出一个 `ClassNotFoundException` 异常。

*反序列化操作2*

另外，当JVM反序列化对象时，能找到class文件，但是class文件在序列化对象之后发生了修改，那么反序列化操作也会失败，抛出一个`InvalidClassException`异常。发生这个异常的原因如下：

- 该类的序列版本号与从流中读取的类描述符的版本号不匹配
    
- 该类包含未知数据类型
    
- 该类没有可访问的无参数构造方法
    

`Serializable` 接口给需要序列化的类，提供了一个序列版本号。`serialVersionUID` 该版本号的目的在于验证序列化的对象和对应类是否版本匹配。

```java
public class Employee implements java.io.Serializable {  
     // 加入序列版本号  
     private static final long serialVersionUID = 1L;  
     public String name;  
     public String address;  
     // 添加新的属性 ,重新编译, 可以反序列化,该属性赋为默认值.  
     public int eid;   
​  
     public void addressCheck() {  
         System.out.println("Address  check : " + name + " -- " + address);  
     }  
}
```


## 3.4 练习：序列化集合

1. 将存有多个自定义对象的集合序列化操作，保存到`list.txt`文件中。
    
2. 反序列化`list.txt` ，并遍历集合，打印对象信息。
    

### 案例分析

1. 把若干学生对象 ，保存到集合中。
    
2. 把集合序列化。
    
3. 反序列化读取时，只需要读取一次，转换为集合类型。
    
4. 遍历集合，可以打印所有的学生信息
    

### 案例实现

```java
public class SerTest {  
    public static void main(String[] args) throws Exception {  
        // 创建 学生对象  
        Student student = new Student("老王", "laow");  
        Student student2 = new Student("老张", "laoz");  
        Student student3 = new Student("老李", "laol");  
​  
        ArrayList<Student> arrayList = new ArrayList<>();  
        arrayList.add(student);  
        arrayList.add(student2);  
        arrayList.add(student3);  
        // 序列化操作  
        // serializ(arrayList);  
          
        // 反序列化    
        ObjectInputStream ois  = new ObjectInputStream(new FileInputStream("list.txt"));  
        // 读取对象,强转为ArrayList类型  
        ArrayList<Student> list  = (ArrayList<Student>)ois.readObject();  
          
        for (int i = 0; i < list.size(); i++ ){  
            Student s = list.get(i);  
            System.out.println(s.getName()+"--"+ s.getPwd());  
        }  
    }  
​  
    private static void serializ(ArrayList<Student> arrayList) throws Exception {  
        // 创建 序列化流   
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("list.txt"));  
        // 写出对象  
        oos.writeObject(arrayList);  
        // 释放资源  
        oos.close();  
    }  
}
```


## 打印流

### 概述

>平时我们在控制台打印输出，是调用`print`方法和`println`方法完成的，这两个方法都来自于`java.io.PrintStream`类，该类能够方便地打印各种数据类型的值，是一种便捷的输出方式。

### `PrintStream`类

*构造方法*

- `public PrintStream(String fileName)` ： 使用指定的文件名创建一个新的打印流。
    

构造举例，代码如下：

```java
PrintStream ps = new PrintStream("ps.txt")；
```


*改变打印流向*

`System.out`就是`PrintStream`类型的，只不过它的流向是系统规定的，打印在控制台上。不过，既然是流对象，我们就可以玩一个"小把戏"，改变它的流向。

```java
public class PrintDemo {  
    public static void main(String[] args) throws IOException {  
        // 调用系统的打印流,控制台直接输出97  
        System.out.println(97);  
        
        // 创建打印流,指定文件的名称  
        PrintStream ps = new PrintStream("ps.txt");  
          
        // 设置系统的打印流流向,输出到ps.txt  
        System.setOut(ps);  
        // 调用系统的打印流,ps.txt中输出97  
        System.out.println(97);  
    }  
}
```


## 压缩流和解压缩流

压缩流：

	负责压缩文件或者文件夹

解压缩流：

	负责把压缩包中的文件和文件夹解压出来

```java
/*  
*   解压缩流  
*  
* */  
public class ZipStreamDemo1 {  
    public static void main(String[] args) throws IOException {  
​  
        //1.创建一个File表示要解压的压缩包  
        File src = new File("D:\\aaa.zip");  
        //2.创建一个File表示解压的目的地  
        File dest = new File("D:\\");  
​  
        //调用方法  
        unzip(src,dest);  
​  
    }  
​  
    //定义一个方法用来解压  
    public static void unzip(File src,File dest) throws IOException {  
        //解压的本质：把压缩包里面的每一个文件或者文件夹读取出来，按照层级拷贝到目的地当中  
        //创建一个解压缩流用来读取压缩包中的数据  
        ZipInputStream zip = new ZipInputStream(new FileInputStream(src));  
        //要先获取到压缩包里面的每一个zipentry对象  
        //表示当前在压缩包中获取到的文件或者文件夹  
        ZipEntry entry;  
        while((entry = zip.getNextEntry()) != null){  
            System.out.println(entry);  
            if(entry.isDirectory()){  
                //文件夹：需要在目的地dest处创建一个同样的文件夹  
                File file = new File(dest,entry.toString());  
                file.mkdirs();  
            }else{  
                //文件：需要读取到压缩包中的文件，并把他存放到目的地dest文件夹中（按照层级目录进行存放）  
                FileOutputStream fos = new FileOutputStream(new File(dest,entry.toString()));  
                int b;  
                while((b = zip.read()) != -1){  
                    //写到目的地  
                    fos.write(b);  
                }  
                fos.close();  
                //表示在压缩包中的一个文件处理完毕了。  
                zip.closeEntry();  
            }  
        }  
        zip.close();  
    }  
}
```

```java
public class ZipStreamDemo2 {  
    public static void main(String[] args) throws IOException {  
        /*  
         *   压缩流  
         *      需求：  
         *          把D:\\a.txt打包成一个压缩包  
         * */  
        //1.创建File对象表示要压缩的文件  
        File src = new File("D:\\a.txt");  
        //2.创建File对象表示压缩包的位置  
        File dest = new File("D:\\");  
        //3.调用方法用来压缩  
        toZip(src,dest);  
    }  
​  
    /*  
    *   作用：压缩  
    *   参数一：表示要压缩的文件  
    *   参数二：表示压缩包的位置  
    * */  
    public static void toZip(File src,File dest) throws IOException {  
        //1.创建压缩流关联压缩包  
        ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(new File(dest,"a.zip")));  
        //2.创建ZipEntry对象，表示压缩包里面的每一个文件和文件夹  
        //参数：压缩包里面的路径  
        ZipEntry entry = new ZipEntry("aaa\\bbb\\a.txt");  
        //3.把ZipEntry对象放到压缩包当中  
        zos.putNextEntry(entry);  
        //4.把src文件中的数据写到压缩包当中  
        FileInputStream fis = new FileInputStream(src);  
        int b;  
        while((b = fis.read()) != -1){  
            zos.write(b);  
        }  
        zos.closeEntry();  
        zos.close();  
    }  
}
```

```java
public class ZipStreamDemo3 {  
    public static void main(String[] args) throws IOException {  
        /*  
         *   压缩流  
         *      需求：  
         *          把D:\\aaa文件夹压缩成一个压缩包  
         * */  
        //1.创建File对象表示要压缩的文件夹  
        File src = new File("D:\\aaa");  
        //2.创建File对象表示压缩包放在哪里（压缩包的父级路径）  
        File destParent = src.getParentFile();//D:\\  
        //3.创建File对象表示压缩包的路径  
        File dest = new File(destParent,src.getName() + ".zip");  
        //4.创建压缩流关联压缩包  
        ZipOutputStream zos = new ZipOutputStream(new FileOutputStream(dest));  
        //5.获取src里面的每一个文件，变成ZipEntry对象，放入到压缩包当中  
        toZip(src,zos,src.getName());//aaa  
        //6.释放资源  
        zos.close();  
    }  
​  
    /*  
    *   作用：获取src里面的每一个文件，变成ZipEntry对象，放入到压缩包当中  
    *   参数一：数据源  
    *   参数二：压缩流  
    *   参数三：压缩包内部的路径  
    * */  
    public static void toZip(File src,ZipOutputStream zos,String name) throws IOException {  
        //1.进入src文件夹  
        File[] files = src.listFiles();  
        //2.遍历数组  
        for (File file : files) {  
            if(file.isFile()){  
                //3.判断-文件，变成ZipEntry对象，放入到压缩包当中  
                ZipEntry entry = new ZipEntry(name + "\\" + file.getName());//aaa\\no1\\a.txt  
                zos.putNextEntry(entry);  
                //读取文件中的数据，写到压缩包  
                FileInputStream fis = new FileInputStream(file);  
                int b;  
                while((b = fis.read()) != -1){  
                    zos.write(b);  
                }  
                fis.close();  
                zos.closeEntry();  
            }else{  
                //4.判断-文件夹，递归  
                toZip(file,zos,name + "\\" + file.getName());  
                //     no1            aaa   \\   no1  
            }  
        }  
    }  
}
```


## 工具包（Commons-io）

介绍：

	Commons是apache开源基金组织提供的工具包，里面有很多帮助我们提高开发效率的API

比如：

	StringUtils   字符串工具类

	NumberUtils   数字工具类

	ArrayUtils   数组工具类

	RandomUtils   随机数工具类

	DateUtils   日期工具类

	StopWatch   秒表工具类

	ClassUtils   反射工具类

	SystemUtils   系统工具类

	MapUtils   集合工具类

	Beanutils   bean工具类

	Commons-io io的工具类

	等等.....

其中：`Commons-io`是`apache`开源基金组织提供的一组有关`IO`操作的开源工具包。

作用：提高`IO`流的开发效率。

使用方式：

1，新建lib文件夹

2，把第三方jar包粘贴到文件夹中

3，右键点击add as a library

代码示例：
```java
public class CommonsIODemo1 {  
    public static void main(String[] args) throws IOException {  
        /*  
          FileUtils类  
                static void copyFile(File srcFile, File destFile)                   复制文件  
                static void copyDirectory(File srcDir, File destDir)                复制文件夹  
                static void copyDirectoryToDirectory(File srcDir, File destDir)     复制文件夹  
                static void deleteDirectory(File directory)                         删除文件夹  
                static void cleanDirectory(File directory)                          清空文件夹  
                static String readFileToString(File file, Charset encoding)         读取文件中的数据变成成字符串  
                static void write(File file, CharSequence data, String encoding)    写出数据  
​  
            IOUtils类  
                public static int copy(InputStream input, OutputStream output)      复制文件  
                public static int copyLarge(Reader input, Writer output)            复制大文件  
                public static String readLines(Reader input)                        读取数据  
                public static void write(String data, OutputStream output)          写出数据  
         */  
​  
​  
        /* File src = new File("myio\\a.txt");  
        File dest = new File("myio\\copy.txt");  
        FileUtils.copyFile(src,dest);*/  
​  
​  
        /*File src = new File("D:\\aaa");  
        File dest = new File("D:\\bbb");  
        FileUtils.copyDirectoryToDirectory(src,dest);*/  
​  
        /*File src = new File("D:\\bbb");  
        FileUtils.cleanDirectory(src);*/  
​  
​  
​  
    }  
}  
```

​

# 7. 工具包（`hutool`）

介绍：

`Commons`是国人开发的开源工具包，里面有很多帮助我们提高开发效率的API

比如：

	DateUtil  日期时间工具类

	TimeInterval  计时器工具类

	StrUtil  字符串工具类

	HexUtil   16进制工具类

	HashUtil   Hash算法类

	ObjectUtil  对象工具类

	ReflectUtil   反射工具类

	TypeUtil  泛型类型工具类

	PageUtil  分页工具类

	NumberUtil  数字工具类

使用方式：

1，新建lib文件夹

2，把第三方jar包粘贴到文件夹中

3，右键点击add as a library

代码示例：

```java
public class Test1 {  
    public static void main(String[] args) {  
    /*  
        FileUtil类:  
                file：根据参数创建一个file对象  
                touch：根据参数创建文件  
​  
                writeLines：把集合中的数据写出到文件中，覆盖模式。  
                appendLines：把集合中的数据写出到文件中，续写模式。  
                readLines：指定字符编码，把文件中的数据，读到集合中。  
                readUtf8Lines：按照UTF-8的形式，把文件中的数据，读到集合中  
​  
                copy：拷贝文件或者文件夹  
    */  
​  
​  
       /* File file1 = FileUtil.file("D:\\", "aaa", "bbb", "a.txt");  
        System.out.println(file1);//D:\aaa\bbb\a.txt  
​  
        File touch = FileUtil.touch(file1);  
        System.out.println(touch);  
​  
​  
        ArrayList<String> list = new ArrayList<>();  
        list.add("aaa");  
        list.add("aaa");  
        list.add("aaa");  
​  
        File file2 = FileUtil.writeLines(list, "D:\\a.txt", "UTF-8");  
        System.out.println(file2);*/  
​  
      /*  ArrayList<String> list = new ArrayList<>();  
        list.add("aaa");  
        list.add("aaa");  
        list.add("aaa");  
        File file3 = FileUtil.appendLines(list, "D:\\a.txt", "UTF-8");  
        System.out.println(file3);*/  
        List<String> list = FileUtil.readLines("D:\\a.txt", "UTF-8");  
        System.out.println(list);  
    }  
}
```
