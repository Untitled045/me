# java IO

<img src="C:\Users\15893\Pictures\Saved Pictures\aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwMTA1MTgyMzQyMjI3.jpg" alt="java IO的类结构" style="zoom:60%;" />

## 0. java的字符流与字节流

- 字符流：一次读入16个二进制位
- 字节流：一次读入8个二进制位
- 字节流可以处理设备上的所有数据，所以字节流一样可以处理字符数据
- **结论：只要是处理纯文本数据，一般优先使用字符流，其余情况都使用字符流**

### 输入字节流

- InputStream是所有字节流的父类，是一个抽象类
- **ByteArrayInputStream、StringBufferInputStream、FileInputStream**是三种基本的介质流，分别从**Byte数组。StringBuffer、和本地文件**中读取数据
- **PipedInputStream**是从与其他线程管道中读取数据
- **ObjectInputStream**和所有**FilterInputStream**的子类都是装饰流

### 输出字节流

- **OutputStream** 是所有的输出字节流的父类，它是一个抽象类

- **ByteArrayOutputStream、FileOutputStream** 是两种基本的介质流，它们分别向**Byte 数组、和本地文件**中写入数据。

- **PipedOutputStream** 是向与其它线程共用的管道中写入数据。

- **ObjectOutputStream** 和所有**FilterOutputStream** 的子类都是装饰流。

### 节点流

节点流：直接与数据源相连，读入或者读出，<u>直接使用节点流读写不方便，为了更快的读写文件才有了处理流</u>

#### 常用的节点流

- 父　类 ：**InputStream 、OutputStream、 Reader、 Writer**
- 文　件 ：**FileInputStream 、 FileOutputStrean 、FileReader 、FileWriter** 文件进行处理的节点流
- 数　组 ：**ByteArrayInputStream、 ByteArrayOutputStream、 CharArrayReader 、CharArrayWriter** 对数组进行处理的节点流（对应的不再是文件，而是内存中的一个数组）
- 字符串 ：**StringReader、 StringWriter** 对字符串进行处理的节点流
- 管　道 ：**PipedInputStream 、PipedOutputStream 、PipedReader 、PipedWriter** 对管道进行处理的节点流

### 处理流

处理流与节点流一块使用，在节点流的基础上，再放上一层。<u>处理流的构造方法总是要带一个其他流对象作为参数</u>。 一个流对象经过其他流的多次包装，称为流的链接。 

#### 常用的处理流

- 缓冲流：**BufferedInputStrean 、BufferedOutputStream、 BufferedReader、 BufferedWrite**r 增加缓冲功能，避免频繁读写硬盘。
- 转换流：**InputStreamReader 、OutputStreamReader**实现字节流和字符流之间的转换。
- 数据流： **DataInputStream 、DataOutputStream** 等-提供将基础数据类型写入到文件中，或者读取出来。

### 转换流

InputStreamReader、OutputStreamWriter要InputStream或OutputStream作为参数，实现从字节流到字符流的转换

```java
InputStreamReader(InputStream in);
InputStreamReader(InputStream in, String charSet);	//charSet为制定编码格式，如果未指定，默认为GBK
OutputStreamWriter(OutputStream out);
OutputStreamWriter(OutputStream out, String charSet);
```

### **什么情况下使用哪种流呢?**

- 如果数据所在的文件通过windows自带的记事本打开并能读懂里面的内容，就用字符流，其他用字节流。
- 如果你什么都不知道，就用字节流。

## 1. File

### File的构造方法

```java
public File(String pathname);
public File(String parent, String child);
public File(File parent, String child);
```

注：一般使用第一种方法

### 创建功能

```java
public boolean createNewFile();	//创建文件
public boolean mkdir();			//创建文件夹
public boolean mkdirs();		//创建多个文件
```

### 删除功能

```java
public boolean delete();		//删除文件或文件夹，不进回收站
```

### 重命名功能

```java
public boolean renameTo(File dest);		//从命名或剪切

File f1 = new File("e:/xyf1.txt");
File f2 = new File("e:/xyf2.txt");
File f3 = new File("xyf.txt");
//重命名
f1.renameTo(f2);
//剪切
f2.renameTo(f3);
```

### 判断功能

```java
public boolean isDirectory(); 	//判断是否有目录
public boolean isFile();		//判断是否是文件
public boolean exists();		//判断是否存在
public boolean canRead();		//判断是否可读
public boolean canWrite();		//判断会否可写
public boolean isHidden();		//判断是否隐藏
```

基本获取功能

```java
public String getAbsolutePath();	//获取绝对路径
public String getPath();			//获取相对路径
public String getName();			//获取文件名
public long length();				//获取长度，字节数
public long lastModified();			//获取最后一次修改时间
```

### 高级获取功能

```java
public String[] list();	//获取指定目录价的所有文件（夹）的名称数组
public File[] listFiles();	//获取指定目录下的所有文件（夹）的File数组
```

#### FileFilter的使用

```java
//获取指定目录下的jpg图片文件
File file = new File("E:")
File[] fs = file.listFiles(new FileFilter(){
   @Override
    public boolean accept(File pathname){
        return pathname.isFile() 
            && pathname.getName().endWith(".jpg");
    }
});
```

- listFiles有两种过滤器FilenameFilter、FileFilter
- list只有FilenameFilter
- 重写过滤器accept

- FilenameFilter  accept(File dir, String name)  dir是父路径，name是文件名

- FileFilter  accept(File pathname) pathname文件的绝对路径

- accept  return true  代表是满足条件的

## 2. Input/OutputStream

### 构造方法

```java
FileOutputStream(File file);
FileOutputStream(String name);
FileInputStream(File file);
FileInputStream(String name);
```

### 通过字节输出流写出数据到文本

```java
public void write(int b)
public void write(byte[] b)
public void write(byte[] b,int off,int len)

outputStream.write("hello".getBytes()); 文本中出现hello
outputStream.write(96)  //文本中出现 a

byte[] bys={97,98,99,100,101};
outputStream.write(bys,1,3); 文本中出现bcd

//追加
FileOutputStream outputStream = new FileOutputStream("a.txt",true);
//第二个参数true设置为可追加

//换行\n\r
for (int i = 0; i <5 ; i++) {
    outputStream.write("hello".getBytes());
    outputStream.write("\n\r".getBytes());
}
```

把刚才写的数据读取到控制台

```java
public int read()
public int read(byte[] b)
    
//读一个字节，读到-1表示读完
int by = 0;
while ((by=inputStream.read())!=-1){
      System.out.println((char)by);
}
//读一个字节数组，一般是1024大小
int len = 0 ;
byte[] bys = new byte[1024];
while ((len = inputStream.read(bys)) != -1) {
    System.out.println(new String(bys,0,len));
}
```

### 字节缓冲流

```java
字节缓冲输出流		BufferedOutputStream
字节缓冲输入流		BufferedInputStream

BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("a.txt",true));
bos.write("hello world".getBytes());
bos.close();

BufferedInputStream bis = new BufferedInputStream(new FileInputStream("a.txt"));
byte[] bytes = new byte[1024];
int len = 0;
while ((len=bis.read(bytes)) != -1) {
    System.out.println(new String(bytes,0,len));
}
bis.close();
```

- 成员方法与字节流基本一样，字节缓冲流的作用就是提高输入输出的效率。
- 构造方法可以指定缓冲区的大小，但是我们一般用不上，因为默认缓冲区大小就足够了。
- 为什么不传递一个具体的文件或者文件路径，而是传递一个OutputStream对象呢?原因很简单，字节缓冲区流仅仅提供缓冲区，为高效而设计的。但是呢，真正的读写操作还得靠基本的流对象实现。

### 复制文件对比实例

```java
public class copy2 {
    //一个字节一个字节的复制，耗时22697毫秒
    public static  void  fun() throws IOException {
        FileInputStream fis = new FileInputStream("F:\\汤包\\慕课大巴\\modern-java.pdf");
        FileOutputStream fos = new FileOutputStream("E:\\modern-java.pdf");
        int by = 0;
        while ((by=fis.read()) != -1) {
            fos.write(by);
        }
        fis.close();
        fos.close();
    }
    //1024字节数组复制 耗时63毫秒
    public  static void  fun1() throws IOException {
        FileInputStream fis = new FileInputStream("F:\\汤包\\慕课大巴\\modern-java.pdf");
        FileOutputStream fos = new FileOutputStream("E:\\modern-java.pdf");
        int len = 0;
        byte[] bytes =new byte[1024];
        while ((len=fis.read(bytes)) != -1) {
            fos.write(bytes,0,len);
        }
        fis.close();
        fos.close();
    }
    // 一个字节一个字节复制，但是用了缓冲流 耗时64毫秒
    public static   void  fun2() throws IOException {
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("E:\\modern-java.pdf"));
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("F:\\汤包\\慕课大巴\\modern-java.pdf"));
        int by = 0;
        while ((by=bis.read()) != -1) {
            bos.write(by);
        }
        bis.close();
        bos.close();
    }
    // 1024字节数组复制并用了缓冲流 耗时7毫秒
    public  static void  fun3() throws IOException {
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("E:\\modern-java.pdf"));
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("F:\\汤包\\慕课大巴\\modern-java.pdf"));
        int len = 0;
        byte[] bytes =new byte[1024];
        while ((len=bis.read(bytes)) != -1) {
            bos.write(bytes,0,len);
        }
        bis.close();
        bos.close();
    }

    public static void main(String args[]) throws IOException {
        long t1 = System.currentTimeMillis();
        fun3();
        long t2 = System.currentTimeMillis();
        System.out.println(t2-t1);
    }

}
```

## 3. 字符流的变革

在IO流中，先诞生了字节流，但是字节流读取数据会出现中文乱码问题

```java
FileInputStream fis = new FileInputStream("a.txt");
int by = 0;
while((by = fis.read()) != -1){
    System.out.print((char)by);		//这种情况会出现中文乱码
}

//解决方案
byte[] by = new byte[1024];
int len = 0;
while((len = fis.read(by)) != -1){
    System.out.print(new String(by, 0, len));
}

```

当然上面的解决方式不是字节流做的，而是String构造方法中的解码功能，且其默认编码为UTF-8。

```java
String(byte[] byte, String charsetName);//通过指定的字符集解码字节数组
byte[] getBytes(String charsetName);	//使用指定的字符集对字符串编码为字节数组
String--》byte[]		编码		把看得懂的变成看不懂的
byte[]--》String		解码		吧看不懂的变成看得懂的
```

虽然字节流有解决乱码的方案，但并不方便，所以引入了转换流：OutputStreamWriter、InputStreamReader

### OutputStreamWriter

```java
OutputStreamWriter(OutputStream out);//使用默认编码1.8为UTF-8转换
OutputStreamWriter(OutputStream out, String charSet);//使用指定的编码转换

public void write(int c):写一个字符
public void write(char[] cbuf):写一个字符数组
public void write(char[] cbuf,int off,int len):写一个字符数组的一部分
public void write(String str):写一个字符串
public void write(String str,int off,int len):写一个字符串的一部分

面试题：close()和flush()的区别?
A:close()关闭流对象，但是先刷新一次缓冲区。关闭之后，流对象不可以继续再使用了。
B:flush()仅仅刷新缓冲区,刷新之后，流对象还可以继续使用。
```

### InputStreamReader

```java
InputStreamReader(InputStream in);
InputStreamReader(InputStream in, String charSet);

int read():一次读取一个字符
int read(char[] chs):一次读取一个字符数组
```

**字符流=字节流+编码表**

OutputStreamWriter = FileOutputStream + 编码表(GBK)

FileWriter = FileOutputStream + 编码表(GBK)

InputStreamReader = FileInputStream + 编码表(GBK)

FileReader = FileInputStream + 编码表(GBK)

### 字符缓冲流

字符流学习字节缓冲流的经验也有缓冲流：BufferedReader、BufferedWriter

```java
//文件复制
BufferedReader br = new BufferedReader(new FileInputStream("a.txt"));
BUfferedWriter bw = new BufferedWriter(new FileOutputStream("b.txt"));
char[] chars = new char[1024];
int len = 0;
 while((len = br.read(chars)) != -1){
 	bw.writer(chars, 0, len);
 	bw.flush();
 }
br.close();
bw.close();

//字符缓冲流还有自己独有的方法
BufferedWriter --- void newLine()---换行
BufferedReader --- String readerLine()---读一行数据
//改写上方的程序
String line = null;
while((line = br.readLine()) != null){
    //readLine不会读取换行符，读到末尾返回null
    bw.write(line);
    bw.newLine();
    bw.flush();
}
```

### LineNumberReader

LineNumberReader继承自BufferReader，且增加了行号的操作

LineNumberWriter比较简单

### IO流总结

<img src="C:\Users\15893\Pictures\Saved Pictures\3417249-e2ac19eb42d1e90d.webp" style="zoom:80%;" />

## 4. IO中的配角

### 1. 数据操作流

```java
DateInputStream
DateOutputStream
//可以操作基本数据类型
```

### 2. 内存操作流

用于处理临时存储信息，程序结束，数据就从内存中消失了

```java
//有时我们操作完毕之后不需要产生文件，就可以使用内存流
ByteArrayInputStream 、 ByteArrayOutputStream
CharArrayReader 、 CharArrayWriter
StringReader 、 StringWriter
    
//String
private static void fun() throw IOException{
    StringWriter sw = new StringWriter();
    sw.write("hello");
    
    StringReader sr = new StringReader();
    int len = 0;
    while((len = sr.read()) != -1){
        System.out.print((char)len);
    }
}
```

注：close（）不必要

### 3. 打印流

```dart
(1)字节打印流，字符打印流
(2)特点：
        A:只操作目的地,不操作数据源
        B:可以操作任意类型的数据
        C:如果启用了自动刷新，在调用println()方法的时候，能够换行并刷新
        D:可以直接操作文件
          问题：哪些流可以直接操作文件呢?
           看API，如果其构造方法能够同时接收File和String类型的参数，一般都			是可以直接操作文件的
(3)复制文本文件
BufferedReader br = new BufferedReader(new FileReader("a.txt"));
PrintWriter pw = new PrintWriter(new FileWriter("b.txt"),true);
        
String line = null;
while((line=br.readLine())!=null) {
    pw.println(line);
}        
pw.close();
br.close();
```

PrintStream是OutputStream的子类，PrintWriter是Writer的子类，两者处于对等的位置上，所以它们的API是非常相似的，区别无非一个是字节打印流，一个是字符打印流。

 **需要注意：字节打印流还是标准输出流的对象哦。** 

### 4. 标准输入输出流

```java
(1)System类下面有这样的两个字段
        in 标准输入流
        out 标准输出流
(2)三种键盘录入方式
        A:main方法的args接收参数
        B:System.in通过BufferedReader进行包装
            BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        C:Scanner
            Scanner sc = new Scanner(System.in);
(3)输出语句的原理和如何使用字符流输出数据
        A:原理
            System.out.println("helloworld");
            
            PrintStream ps = System.out;
            ps.println("helloworld");
        B:把System.out用字符缓冲流包装一下使用
            BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
```

### 5. 随机访问流

```java
/* 随机访问流可以按照文件指针的位置写数据的读数据
 * 
 * 随机访问流：
 *      RandomAccessFile类不属于流，是Object类的子类。
 *      但它融合了InputStream和OutputStream的功能。
 *      支持对文件的随机访问读取和写入。
 *
 * public RandomAccessFile(String name,String mode)：第一个参数是文件路径，第二个参数是操作文件的模式。
 *      模式有四种，我们最常用的一种叫"rw",这种方式表示我既可以写数据，也可以读取数据。
 访问模式：
 "r" 以只读方式打开。调用结果对象的任何 write 方法都将导致抛出 IOException。  
"rw" 打开以便读取和写入。如果该文件尚不存在，则尝试创建该文件。  
"rws" 打开以便读取和写入，对于 "rw"，还要求对文件的内容或元数据的每个更新都同步写入到底层存储设备。  
"rwd" 打开以便读取和写入，对于 "rw"，还要求对文件内容的每个更新都同步写入到底层存储设备。 
一般使用rw模式。
 
 */
public class RandomAccessFileDemo {

    public static void main(String[] args) throws IOException {
        // write();
        read();
    }

    private static void read() throws IOException {
        // 创建随机访问流对象
        RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

        int i = raf.readInt();
        System.out.println(i);
        // 该文件指针可以通过 getFilePointer方法读取，并通过 seek 方法设置。
        System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

        char ch = raf.readChar();
        System.out.println(ch);
        System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

        String s = raf.readUTF();
        System.out.println(s);
        System.out.println("当前文件的指针位置是：" + raf.getFilePointer());

        // 我不想重头开始了，我就要读取a，怎么办呢?
        raf.seek(4);
        ch = raf.readChar();
        System.out.println(ch);
    }

    private static void write() throws IOException {
        // 创建随机访问流对象
        RandomAccessFile raf = new RandomAccessFile("raf.txt", "rw");

        // 怎么玩呢?
        raf.writeInt(100);
        raf.writeChar('a');
        raf.writeUTF("中国");

        raf.close();
    }
}

```

### 6. 合并流

```java
(1)把多个输入流的数据写到一个输出流中。
(2)构造方法：
   A:SequenceInputStream(InputStream s1, InputStream s2) 
   B:SequenceInputStream(Enumeration<? extends InputStream> e)
```

合并复制案例：

```java
/*复制：
 * a.txt+b.txt -- ab.txt
 */
public class SequenceInputStreamDemo {
    public static void main(String[] args) throws IOException {
        FileInputStream f1 = new FileInputStream("a.txt");
        FileInputStream f2 = new FileInputStream("b.txt");
        SequenceInputStream sis = new SequenceInputStream(f1,f2);

        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("ab.txt"));
        byte[] bytes = new byte[1024];
        int len = 0 ;
        while ((len = sis.read(bytes)) != -1) {
            bos.write(bytes,0,len);
        }

        bos.close();
        sis.close();

    }
}

/*
 * 以前的操作：
 * a.txt+b.txt+c.txt -- abc.txt
 */
public class SequenceInputStreamDemo2 {
    public static void main(String[] args) throws IOException {
        Vector<InputStream> v = new Vector<>();
        InputStream s1 = new FileInputStream("a.txt");
        InputStream s2 = new FileInputStream("b.txt");
        InputStream s3 = new FileInputStream("c.txt");
        v.add(s1);
        v.add(s2);
        v.add(s3);
        Enumeration<InputStream> en = v.elements();
        SequenceInputStream sis = new SequenceInputStream(en);
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("abc.txt"));
        byte[] bytes = new byte[1024];
        int len = 0 ;
        while ((len = sis.read(bytes)) != -1) {
            bos.write(bytes,0,len);
        }
        bos.close();
        sis.close();
    }
}
```



### ***7. 序列化流***

(1)可以把对象写入文本文件或者在网络中传输
(2)如何实现序列化呢?
        让被序列化的对象所属类实现序列化接口。
        该接口是一个标记接口。没有功能需要实现。
(3)注意问题：
        把数据写到文件后，在去修改类会产生一个问题。
        如何解决该问题呢?
            在类文件中，给出一个固定的序列化id值。
            而且，这样也可以解决黄色警告线问题
(4)面试题：
        什么时候序列化? 
        如何实现序列化?
        什么是反序列化?
    	序列化（Serialization）是将对象的状态信息转化为可以存储或者传输的形式的过程，一般将一个对象存储到一个储存媒介，例如档案或记忆体缓冲等，在网络传输过程中，可以是字节或者XML等格式；而字节或者XML格式的可以还原成完全相等的对象，这个相反的过程又称为反序列化。

​    	在Java中，我们可以通过多种方式来创建对象，并且只要对象没有被回收我们都可以复用此对象。但是，我们创建出来的这些对象都存在于JVM中的堆（heap）内存中，只有JVM处于运行状态的时候，这些对象才可能存在。一旦JVM停止，这些对象也就随之消失；

​		<u>对象序列化机制（object serialization）是java语言内建的一种对象持久化方式</u>，通过对象序列化，可以将对象的状态信息保存未字节数组，并且可以在有需要的时候将这个字节数组通过反序列化的方式转换成对象，对象的序列化可以很容易的在JVM中的活动对象和字节数组（流）之间进行转换。

​		在JAVA中，对象的序列化和反序列化被广泛的应用到<u>RMI（远程方法调用）及网络传输中。</u>

```java
/*
 * NotSerializableException:未序列化异常
 *
 * 类通过实现 java.io.Serializable 接口以启用其序列化功能。未实现此接口的类将无法使其任何状态序列化或反序列化。
 * 该接口居然没有任何方法，类似于这种没有方法的接口被称为标记接口。
 *
 * java.io.InvalidClassException:
 * cn.itcast_07.Person; local class incompatible:
 * stream classdesc serialVersionUID = -2071565876962058344,
 * local class serialVersionUID = -8345153069362641443
 *
 * 为什么会有问题呢?
 *      Person类实现了序列化接口，那么它本身也应该有一个标记值。
 *      这个标记值假设是100。
 *      开始的时候：
 *      Person.class -- id=100
 *      wirte数据： oos.txt -- id=100
 *      read数据: oos.txt -- id=100
 *
 *      现在：
 *      Person.class -- id=200
 *      wirte数据： oos.txt -- id=100
 *      read数据: oos.txt -- id=100
 *
 * 我们要知道的是：
 *      看到类实现了序列化接口的时候，要想解决黄色警告线问题，就可以自动产生一个序列化id值。
 *      而且产生这个值以后，我们对类进行任何改动，它读取以前的数据是没有问题的。
 *
 * 注意：
 *      我一个类中可能有很多的成员变量，有些我不想进行序列化。请问该怎么办呢?
 *      使用transient关键字声明不需要序列化的成员变量
 */
public class Person implements Serializable {
    private static final long serialVersionUID = 5816344743154801933L;
    private String name;

    // private int age;

    private transient int age;

    // int age;

    public Person() {
        super();
    }

    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + "]";
    }
}
```

```java
/*
 * 序列化流：把对象按照流一样的方式存入文本文件或者在网络中传输。对象 -- 流数据(ObjectOutputStream)
 * 反序列化流:把文本文件中的流对象数据或者网络中的流对象数据还原成对象。流数据 -- 对象(ObjectInputStream)
 */
public class ObjectStreamDemo {
    public static void main(String[] args) throws IOException,
            ClassNotFoundException {
        // 由于我们要对对象进行序列化，所以我们先自定义一个类
        // 序列化数据其实就是把对象写到文本文件
        // write();

        read();
    }

    private static void read() throws IOException, ClassNotFoundException {
        // 创建反序列化对象
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream(
                "oos.txt"));

        // 还原对象
        Object obj = ois.readObject();

        // 释放资源
        ois.close();

        // 输出对象
        System.out.println(obj);
    }

    private static void write() throws IOException {
        // 创建序列化流对象
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(
                "oos.txt"));

        // 创建对象
        Person p = new Person("林青霞", 27);

        // public final void writeObject(Object obj)
        oos.writeObject(p);

        // 释放资源
        oos.close();
    }
}
```

### 8. Properties

```tsx
    (1)是一个集合类，Hashtable的子类
    (2)特有功能
        A:public Object setProperty(String key,String value)
        B:public String getProperty(String key)
        C:public Set<String> stringPropertyNames()
    (3)和IO流结合的方法
        把键值对形式的文本文件内容加载到集合中
        public void load(Reader reader)
        public void load(InputStream inStream)

        把集合中的数据存储到文本文件中
        public void store(Writer writer,String comments)
        public void store(OutputStream out,String comments)
```