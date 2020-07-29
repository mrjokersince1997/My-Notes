#  I/O   

---

## 序列化

### 对象持久化

在 Java 程序中所创建的对象都保存在内存中，一旦 JVM 停止运行，这些对象都将会消失。因此以下两种情况必须通过序列化实现：

1. 需要把对象持久化保存在文件中，在 JVM 重启后能够继续使用。
2. 通过网络传送对象信息，在多个 JVM 间共享对象。

### Serializable 接口

在类中声明实现 Serializable 接口，表示允许 Java 程序对这个类的对象序列化：JVM 会将对象的成员变量保存为一组字节，这些字节可以再被 JVM 组装成对象。对象序列化只保存的对象的成员变量，且不会关注类中的静态变量。

1. **transient 字段**：默认序列化机制就会被忽略。
2. **private 字段**：序列化后不会被保护，任何 JVM 都可读取。
    

```java
//person类的读入读出
//对于 class Person implements Serializable

        ObjectOutputStream oout = new ObjectOutputStream(new FileOutputStream(file));
        Person person = new Person("John", 101, Gender.MALE);
        oout.writeObject(person);
        oout.close();

        ObjectInputStream oin = new ObjectInputStream(new FileInputStream(file));
        Object newPerson = oin.readObject(); // 没有强制转换到Person类型
        oin.close();
```



---

## 标准输入/输出                              

### 标准输入流 System.in

读取标准输入设备数据（键盘），每次输入将以换行符结束。数据类型为 InputStream。

```java
char c = (char)System.in.read();   // 读取单个输入字符，返回其 ASCII 值(int)

byte[] b = new byte[20];
System.in.read(b);                 // 读取输入定长字符组，返回字符个数(int)
```


### 标准输出流 System.out

向标准输出设备输出数据（控制台）。数据类型为 PrintStream。

```java 
System.out.print("hello");                         // 输出数据
System.out.println("hello");                       // 输出数据并换行
```

**格式化输出**

通过 printf 方法可以输出指定格式数据：其中 `%d` 表示整型数字， `%f` 表示浮点型数字， `%%` 表示百分号。

在百分号后加入特殊符号，可以指定数据的显示类型。

符号|作用|示例|效果
-|-|-|-
+|为正数或者负数添加符号|("%+d",99)|+99
2|位数（默认右对齐）|("%4d", 99)|__99
−|左对齐|("%-4d", 99)|99__
0|数字前补0|("%04d", 9999)|0099
,|以“,”对数字分组|("%,d", 9999)|9,999
.2|小数点后精确位数|("%5.2f", 9.999)|_9.99

```java
System.out.printf("The number is %+,9.3f", PI);  // 输出指定格式数据
```

---

## 流输入输出

java.io 文件夹内提供了 Java 程序中 I/O 操作使用的类，使用时需要进行导入。    

### 字节流

#### InputStream/OutputStream 类

以字节为单位进行读取的数据流。常用来处理二进制数据的输入输出，如键盘输入、网络通信。但字节流不能正确显示 Unicode 字符。

**输入流**

```java
InputStream in = new InputStream(socket.getIntputStream());        // 创建输入对象

int len = in.available();                                          // 读取输入对象长度

char c = (char)in.read();                                          // 读取输入字节

byte[] b = new byte[len];                                          // 连续读取输入字节
in.read(b);

in.close();                                                        // 关闭输入对象
```

**输出流**

```java
OutputStream out = new OutputStream(socket.getOutputStream());     // 创建输出对象

byte[] b = {1,2,3};                                                // 导入输出字节          
out.write(b);

out.flush();                                                       // 刷新输出对象，输出字节

out.close();                                                       // 关闭输出对象，输出字节
```


### 字符流

#### Reader/Writer 类

以字符为单位进行读取的数据流。只能用于处理文本数据。且所有文本数据，即经过 Unicode 编码的数据都必须以字符流的形式呈现。

我们在 Java 程序中处理数据往往需要用到字符流，但在通信中却需要使用字节流。这就需要进行数据格式转化。

#### InputStreamReader 类

Reader 类子类。将字节流数据转换成字符流，常用于读取控制台输入或读取网络通信。可指定编码方式，否则使用 IDE 默认编码方式。

```java
// 读取键盘输入
InputStreamReader in = new InputStreamReader(System.in);
// 读取套接字通信，并指定编码格式
InputStreamReader in = new InputStreamReader(socket.getInputStream(), "UTF-8");
```

#### OutputStreamWriter 类

Writer 类子类。将字符流数据转换成字节流，常用于发送网络通信。

```java
// 数据转化为字节流发送
OutputStreamWriter out = new OutputStreamWriter(socket.getOutputStream());
```

### 文件流

#### File 类

用于文件或者目录的描述信息，默认加载当前目录。

```java
File f1 = new File("FileTest.txt");                // 读取当前目录文件
File f2 = new File("D://file//FileTest.txt");      // 读取指定目录文件
```

#### FileInputStream/FileReader 类

FileInputStream 类读取字节流文件信息，FileReader 类读取字符流文件信息。

```java
public class TestFileReader {
    public static void ReadFile(String textName) {
        int c = 0;
        try {
            // 连接文件
            FileReader fr = new FileReader("D:\\Workspaces" + textName);
            // 执行操作
            while ((c = fr.read()) != -1) {
                System.out.print((char)c);
            }
            fr.close();
        } catch (FileNotFoundException e) {
            System.out.println("找不到指定文件");
        } catch (IOException e) {
            System.out.println("文件读取错误");
        }
    }
}
```

#### FileOutputStream/FileWriter 类

FileOutputStream 写入字节流文件信息，FileWriter 类写入字符流文件信息。

```java
public class TestFileWriter {
    public static void ReadFile(String textName) {
        int c = 0;
        try {
            // 追加模式，写入文本信息会添加到文本尾部
            FileWriter fw = new FileWriter(textName);            
            // 覆盖模式，写入文本信息会覆盖原有数据
            FileWriter fw2 = new FileWriter("data.txt", false);
            // 执行操作
            fw.write("Hello world！欢迎来到 java 世界\n");                 
            fw.append("我是下一行");                            
            fw.flush();                                       
            System.out.println("文件编码为" + fw.getEncoding());
            fw.close();                    
        } catch (FileNotFoundException e) {
            System.out.println("找不到指定文件");
        } catch (IOException e) {
            System.out.println("文件写入错误");
        }
    }
}
``` 


### 缓冲流

#### BufferedInputStream/BufferedReader 类

BufferedInputStream 类将输入字节数据暂存到缓冲区数组，BufferedReader 类将输入字符流数据暂存到缓冲区数组。

JVM 在缓冲区数组满后一次性获取缓冲区内的数据，减少了对 CPU 的频繁请求。

#### BufferedOutputStream/BufferedWriter 类

BufferedOutputStream 类将输出字节数据暂存到缓冲区数组，BufferedWriter 类将输出字符流数据暂存到缓冲区数组。

JVM 在刷新时一次性将缓冲区内的数据输出到外部设备，减少了对 CPU 的频繁请求。


```java
class TestBuffer{
    public static void bufferUse() throws IOException {
        // 通过缓冲区读取键盘输入
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        // 通过缓冲区输出到文件
        BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"));
        // 执行操作
        String line = null;
        while((line = br.readLine()) != null){     // readLine 缓冲流特有方法，一次性读取一行
            if("over".equals(line)){
                break;
            }
            bw.write(line);
            bw.newLine();                          // newLine 缓冲流特有方法，写入换行符
            bw.flush();
        }
        bw.close();
        br.close();
    }
}
```



---

## 扫描器

### Scanner 类

包装输入并自动分割数据，调用 next 方法捕获，可以自动转换数据类型。位于 java.util 包内，使用时需进行导入。

```java
Scanner sc = new Scanner(System.in);                             // 读取键盘输入，返回 String 数据类型                  
Scanner sc = new Scanner(new FileInputStream("example.txt"));     // 读取文件信息，返回 String 数据类型

sc.hasNextInt();

int n = sc.nextInt();                                            // 截取数据并自动转化数据类型
String str = sc.nextLine();                                      // 取出行内全部数据

sc.close();                                                      // 关闭 Scanner 类
```


