# Java Net

java.net 文件夹内提供了 Java 程序中网络通信使用的类，使用时需要进行导入。    

---


## HTTP通信

### URL 类

- **URL 类**：用于定向资源所在位置，资源由 URLConnection 类读取。

- **URLConnection 类**：用于读取和写入 URL 类定向的资源，在 HTTP 通信中常用 HttpURLConnection 子类。

在 HTTP 通信中通常需要执行以下五个过程。

1. 创建连接对象。
2. 设置连接参数和请求属性。
3. 建立连接，输出流发送请求。 
4. 输入流读取返回内容。
5. 关闭连接。

### 执行过程

#### 创建连接

```java
// 创建 URL 对象，如果 url 格式错误则抛出 IOException
URL myUrl = new URL("https://www.baidu.com");                   
// 创建 URLConnection 对象，读取 URL 资源
HttpURLConnection myCon = (HttpURLConnection)myUrl.openConnetcion();
```


#### 配置连接


**设置连接参数**

```java
myCon.setRequestMethod("POST");               // 设置连接方法，默认使用 GET
myCon.setDoInput(true);                       // （默认）允许进行字符流输入，执行 read 操作
myCon.setDoOutput(true);                      // （默认）允许进行字符流输出，执行 write 操作
myCon.setUseCaches(false);                    // 设置是否使用缓存
myCon.setConnectTimeout(1000);                 // 设置最长建立连接时间，超时抛出 SocketTimeoutException
myCon.setReadTimeout(1000);                 // 设置最长数据读取时间，超时抛出 SocketTimeoutException
```


**设置请求属性**

```java
// 设置版本
myCon.setRequestProperty("version", "1.2.3");         
// 设置浏览器类型（用于爬虫伪装）
myCon.setRequestProperty("user-agent", "Mozilla/5.0 (compatible;  Windows NT 5.1;SV1)");
// 设置发送文本类型
myCon.setRequestProperty("Content-Type", "application/json;charset=utf-8");
```



#### 连接 & 发送请求

连接和发送请求有两种方式：

1. 调用 connect 方法，直接建立连接并发送请求。
2. 调用 getOutputStream 方法，在输出流中写入数据，在关闭输出流时自动建立连接并发送输出流请求。

```java
OutputStreamWriter out = new OutputStreamWriter(myCon.getOutputStream());
out.write(str);            // 写入数据      
out.close();               // 建立连接并发送请求       
```

#### 获取响应数据

```java
int code = myCon.getResponseCode();            // 获取响应码
String head = myCon.getHeaderField();          // 获取响应头字段
```

获取响应体数据有两种方式：

1. 调用 getContent 方法，直接获取响应内容。
2. 调用 getInputStream 方法，通过输入流获取响应内容。

```java
BufferedReader in = new BufferedReader(new InputStreamReader(myCon.getInputStream()));
while ((str = in.readLine()) != null) {
    System.out.println(str);
}
in.close();
// 关闭连接
myCon.disconnect();
```


### 使用范例


```java
//示例：网络爬虫

public class WebCrawler{
    public static void getHttpJson(int i) {
        try {
            //配置URL
            URL myUrl = new URL("https://static-data.eol.cn/www/school/"+ i + "/info.json");
            //配置连接
            HttpURLConnection myCon = (HttpURLConnection) myUrl.openConnection();
            myCon.setRequestProperty("user-agent", "Mozilla/5.0 (compatible; MSIE 6.0; Windows NT 5.1;SV1)");
            myCon.setConnectTimeout(10000);
            myCon.setReadTimeout(1000);
            //建立连接
            myCon.connect();
            //如果连接成功
            if (myCon.getResponseCode() == 200) {
                System.out.print("ID" + i + "的数据读取成功，数据内容：");
                //读取返回数据
                InputStream in = myCon.getInputStream();
                int cnt = in.available();
                byte[] b = new byte[cnt];
                in.read(b);
                String str = new String(b);
                //输出返回数据
                System.out.println(str);
            }else{
                System.out.println("ID" + i + "的数据读取失败，代码：" + myCon.getResponseCode());
            }
        } catch (MalformedURLException e) {
            System.out.println("URL错误，无法查找到资源。");
        } catch(SocketTimeoutException e){
            System.out.println("ID" + i + "的数据访问连接超时。");
        } catch (IOException e) {
            System.out.println("发送的请求存在错误，资源拒绝访问。");
        }
        return;
    }
}
```

---

## TCP通信

### Socket 类

- **Socket 类**：通过套接字指向对方通信位置，用于建立 TCP 连接。
- **ServerSocket 类**：在服务器建立，绑定自身的端口号用于监听是否有用户请求。

**服务器端**

1. 创建 ServerSocket 对象，绑定监听端口监听客户端请求。
2. 接收到客户端请求后，创建 Socket 与该客户建立专线连接。
3. 双方通过输入输出流进行对话。
4. 关闭流和套接字，继续等待新的连接。

**客户端**

1. 创建 Socket 对象，指明需要连接的服务器地址和端口号。
2. 连接建立后，通过输出流向服务器发送请求信息。
3. 双方通过输入输出流进行对话。
4. 关闭流和套接字。


### 执行过程

#### 创建对象

```java
// 客户端
Socket socket = new Socket("127.0.0.1", 55533);     // 创建 Socket 对象指向服务器


// 服务器
ServerSocket server = new ServerSocket(55533);      // 创建 ServerSocket 对象监听接口
while(true) {
    Socket socket = server.accept();                // 阻塞进程，直到接收到客户端请求返回 Socket 对象
}
```


#### 进行对话

输出流负责输出信息，在另一端输入流将得到输入。失败则抛出 IOException。

```java
//打开输出流
OutputStream out = socket.getOutputStream();
//输出信息（字节流）
String message = "Hello";
out.write(message.getBytes("UTF-8"));
out.write("end");
//关闭输出流
out.close();
```

输入流负责输入信息，得到另一端输出流信息。失败则抛出 IOException。


```java
//打开输入流（转化为字节流被缓冲流读取）
BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream(),"UTF-8"));
//输入信息(接收到end字符则结束)
StringBuilder message = new StringBuilder();
while ((str = in.readLine()) != null && "end".equals(str)) {
  message.append(str);
}
System.out.println("get message: " + message);
//关闭输入流
in.close(); //socket.shutdownOutput();
```

### 使用范例

- **客户端**

```java
public class SocketClient {
  public static void main(String args[]) throws Exception {
    //与本地服务器端建立连接
    String host = "127.0.0.1"; 
    int port = 55533;
    Socket socket = new Socket(host, port);

    //控制台输入并向服务器端输出
    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in, "UTF-8"));      
    BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));         
    while (str != "end") {
      String str = bufferedReader.readLine();
      bufferedWriter.write(str + "\n");
      bufferedWriter.flush();
    }

    //关闭连接
    outputStream.close();
    socket.close();
  }
}
```

- **服务器端**

```java
public class SocketServer {
  public static void main(String[] args) throws Exception {
    // 监听指定的端口
    int port = 55533;
    ServerSocket server = new ServerSocket(port);
    
    //循环等待请求
    while(true){
      //建立连接
      Socket socket = server.accept();
      //从socket中获取输入流并读取
      BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream(), "UTF-8"));
      String str;，
      while ((str = bufferedReader.readLine()) != null) {
        System.out.println("get message from client:" + str);
      }
      //关闭连接
      inputStream.close();
      socket.close();
    }
  }
}
```



#### 多线程

服务器端可以通过多线程实现伪异步，每接受一个 Socket 请求，就创建一个线程来处理它。防止程序被一个请求阻塞。

```java
//服务器端（线程池管理）

public class SocketServer {
    public static void main(String[] args) throws IOException {
        // 监听指定的端口
        int port = 55533;
        ServerSocket server = new ServerSocket(port);
        //创建一个线程池
        ExecutorService executorService = Executors.newFixedThreadPool(100);
        //循环等待请求
        while (true) {
            //建立连接
            Socket socket = serverSocket.accept();
            //分配线程
            Runnable runnable = () -> {
                BufferedReader bufferedReader = null;
                //从socket中获取输入流并读取
                try {
                    bufferedReader = new BufferedReader(new InputStreamReader(socket.getInputStream(), "UTF-8"));
                    String str;
                    while ((str = bufferedReader.readLine()) != null) {
                        System.out.println("get message from client:" + str);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            };
            executorService.submit(runnable);
        }
    }
}
```

#### 流输入输出

在实际应用中，我们通过是采用数据长度+类型+数据的方式来告知一次流输入完成，方便进行后续操作。



https://blog.csdn.net/qq_33865313/article/details/79363640


https://www.cnblogs.com/huanzi-qch/p/9889521.html



### WebSocket 类

Socket 消息推送机制中，用的都是 Ajax 轮询。在特定的时间间隔由客户端自动发出请求，将服务器的消息主动拉回来（服务器启动一个线程去监听与此客户端的通信），这种方式是非常消耗资源的，因为它本质还是 http 请求，而且显得非常笨拙：服务端不能主动向客户端推送数据。

`import javax.websocket.*;`

WebSocket 在浏览器和服务器完成一个握手的动作，在建立连接之后，服务器可以主动传送数据给客户端，客户端也可以随时向服务器发送数据。多客户端、涉及有界面的聊天建议使用websocket（嵌入到了浏览器的内核中）。

https://www.cnblogs.com/interdrp/p/7903736.html




**Spring框架**

服务端：

 1、添加Jar包依赖：

 2、创建一个WebSocket服务端类，并添加@ServerEndpoint(value = "/websocket")注解
表示将 WebSocket 服务端运行在 ws://[Server 端 IP 或域名]:[Server 端口]/项目名/websocket 的访问端点

 3、实现onOpen、onClose、onMessage、onError等方法

客户端

 1、添加Jar包依赖：

 2、创建WebSocket客户端类并继承WebSocketClient

 3、实现构造器，重写onOpen、onClose、onMessage、onError等方法

 **Maven依赖**
```xml
<dependency>
    <groupId>javax.websocket</groupId>
    <artifactId>javax.websocket-api</artifactId>
    <version>1.1</version>
    <scope>provided</scope>
</dependency>

<dependency>
    <groupId>javax</groupId>
    <artifactId>javaee-api</artifactId>
    <version>7.0</version>
    <scope>provided</scope>
</dependency>
```


**服务器定义WebSocket类**

```java
@ServerEndpoint("/websocket/{username}")  
public class WebSocket {  
  
    private static int onlineCount = 0;  
    private static Map<String, WebSocket> clients = new ConcurrentHashMap<String, WebSocket>();  
    private Session session;  
    private String username;  
      
    @OnOpen  
    public void onOpen(@PathParam("username") String username, Session session) throws IOException {  
  
        this.username = username;  
        this.session = session;  
          
        addOnlineCount();  
        clients.put(username, this);  
        System.out.println("已连接");  
    }  
  
    @OnClose  
    public void onClose() throws IOException {  
        clients.remove(username);  
        subOnlineCount();  
    }  
  
    @OnMessage  
    public void onMessage(String message) throws IOException {  
  
        JSONObject jsonTo = JSONObject.fromObject(message);  
          
        if (!jsonTo.get("To").equals("All")){  
            sendMessageTo("给一个人", jsonTo.get("To").toString());  
        }else{  
            sendMessageAll("给所有人");  
        }  
    }  
  
    @OnError  
    public void onError(Session session, Throwable error) {  
        error.printStackTrace();  
    }  
  
    public void sendMessageTo(String message, String To) throws IOException {  
        // session.getBasicRemote().sendText(message);  
        //session.getAsyncRemote().sendText(message);  
        for (WebSocket item : clients.values()) {  
            if (item.username.equals(To) )  
                item.session.getAsyncRemote().sendText(message);  
        }  
    }  
      
    public void sendMessageAll(String message) throws IOException {  
        for (WebSocket item : clients.values()) {  
            item.session.getAsyncRemote().sendText(message);  
        }  
    }  
          
  
    public static synchronized int getOnlineCount() {  
        return onlineCount;  
    }  
  
    public static synchronized void addOnlineCount() {  
        WebSocket.onlineCount++;  
    }  
  
    public static synchronized void subOnlineCount() {  
        WebSocket.onlineCount--;  
    }  
  
    public static synchronized Map<String, WebSocket> getClients() {  
        return clients;  
    }  
}  

```

客户端在JavaScript中调用即可

```javascript
var websocket = null;  
var username = localStorage.getItem("name");  
  
//判断当前浏览器是否支持WebSocket  
if ('WebSocket' in window) {  
    websocket = new WebSocket("ws://" + document.location.host + "/WebChat/websocket/" + username + "/"+ _img);  
} else {  
    alert('当前浏览器 Not support websocket')  
}  
  
//连接发生错误的回调方法  
websocket.onerror = function() {  
    setMessageInnerHTML("WebSocket连接发生错误");  
};  
  
//连接成功建立的回调方法  
websocket.onopen = function() {  
    setMessageInnerHTML("WebSocket连接成功");  
}  
  
//接收到消息的回调方法  
websocket.onmessage = function(event) {  
    setMessageInnerHTML(event.data);  
}  
  
//连接关闭的回调方法  
websocket.onclose = function() {  
    setMessageInnerHTML("WebSocket连接关闭");  
}  
  
//监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。  
window.onbeforeunload = function() {  
    closeWebSocket();  
}  
  
//关闭WebSocket连接  
function closeWebSocket() {  
    websocket.close();  
}  
```

发送消息只需要使用websocket.send("发送消息")，就可以触发服务端的onMessage()方法，当连接时，触发服务器端onOpen()方法，此时也可以调用发送消息的方法去发送消息。关闭websocket时，触发服务器端onclose()方法，此时也可以发送消息。





```java

WebSocket ws = new WebSocket();

JSONObject jo = new JSONObject();
jo.put("message", "这是后台返回的消息！");
jo.put("To",invIO.getIoEmployeeUid());
ws.onMessage(jo.toString());
```

```java
public class MyTest{

　　public static void main(String[] arg0){
　　　　MyWebSocketClient myClient = new MyWebSocketClient("此处为websocket服务端URI");
　　　　// 往websocket服务端发送数据
　　　　myClient.send("此为要发送的数据内容");
　　}

}
```

