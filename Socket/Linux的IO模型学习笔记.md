# IO学习笔记

## 1、BIO

每来一个连接都开启一个线程去进行处理

优势：可以接受很多的连接

问题：线程内存浪费、CPU调度消耗

根源：进行系统调用时，是blocking阻塞的：accept、recv

解决方案：Non-Blocking非阻塞

```shell
#伪代码
socket = 3				#先开启socket，返回文件描述符3
bind(3, 8080)			#将文件描述符3绑定到8080端口
listen(3)				#监听3

while(true)				#死循环
accept(3, ...) = 5		#系统调用accept，从3中阻塞等待客户端的连接，此处已有连接，用文件描述符5指代
...
recv(5, ...)			#从5中阻塞等待IO操作
```

```java

```

## 2、NIO

一个线程可以处理多个连接

优势：规避多线程的问题

弊端：用户态的轮询耗费CPU资源。假设1万个连接，只有一个发来数据，那么有9999次是无意义的，浪费时间和

资源，复杂度在系统调用上

解决方案：改单个为批量，也就是多路复用

```shell
#伪代码
socket = 3				#先开启socket，返回文件描述符3
bind(3, 8080)			#将文件描述符3绑定到8080端口
listen(3)				#监听3
3.nonblocking			#将3设置为非阻塞

while(true)				#死循环
accept(3, ...) = 5		#系统调用accept，从3中阻塞等待客户端的连接，此处已有连接，用文件描述符5指代
5.nonblocking			#将5设置为非阻塞
client_fds.add(5)		#将5放进文件描述符集合

for{
recv(client_fd, ...)    #将文件描述符集合进行轮询，用recv函数进行判断是否有数据读取
}
```

```java
//java代码
public class Test {

    public static void main(String[] args) throws Exception {
        LinkedList<SocketChannel> clients = new LinkedList<>();

        ServerSocketChannel ss = ServerSocketChannel.open();
        ss.bind(new InetSocketAddress(8080));
        ss.configureBlocking(false);    //NIO核心，设置为非阻塞

        while (true){
            SocketChannel client = ss.accept();  //不会阻塞，会根据有无客户端连接返回对应的值
            //进行了系统内核调用：accept。没有客户端连接进来，BIO会一直阻塞，但NIO，不阻塞会立刻返回-1
            //如果客户端连接进来，accept返回的是这个客户端的fd

            if (client == null){
                System.out.println("null......");
            } else {
                client.configureBlocking(false); //服务端的listen socket连接请求三次握手后
                int port = client.socket().getPort();
                System.out.println("client...port:" + port);
                clients.add(client);
            }

            ByteBuffer buffer = ByteBuffer.allocateDirect(4096);

            //遍历已经连接进来的客户端能不能读写数据
            for (SocketChannel channel : clients){ //串行化，可以考虑多线程
                int num = channel.read(buffer); // >0 -1 0 不会阻塞
                if (num > 0){
                    buffer.flip();
                    byte[] bytes = new byte[buffer.limit()];
                    buffer.get(bytes);

                    System.out.println(channel.socket().getPort() + ":" + new String(bytes));
                    buffer.clear();
                }
            }
        }
    }
}
```

## 3、多路复用器：select、poll

**注意：多路复用器，只能给你哪些fds是可读写的状态，仍然需要程序自己去进行读取IO操作。因此无论是BIO、**

**NIO、多路复用等都属于同步IO模型**

优势：进行一次系统调用，把fds传递给内核，内核进行遍历，大大减少无谓的系统调用次数

弊端：1、当fds量大的时候，会存在重复传递给fds。解决方案：内核开辟空间保留fds。2、每次select、poll都要

重新遍历全量的fd。解决方案：中断、callback、增强

```shell
#伪代码
socket = 3				#先开启socket，返回文件描述符3
bind(3, 8080)			#将文件描述符3绑定到8080端口
listen(3)				#监听3

select(fds)/poll(fds)	#时间复杂度O(1)，将所有的文件描述符传给select/poll进行状态判定，返回可读写的fd
accept(fd)				#最终还是要程序自行调用accept和recv，因此属于同步IO模型
recv(fd)
```

## 4、多路复用器epoll

多路复用器epoll正好解决了select和poll的弊端

```shell
#伪代码
socket = 3				#先开启socket，返回文件描述符3
bind(3, 8080)			#将文件描述符3绑定到8080端口
listen(3)				#监听3

epoll_create() = 7		#开辟内存空间，返回文件描述符7。7是一棵红黑树
cpoll_ctl(7, ADD, 3, accept)	#将socket 3 放到内存空间里面去
epoll_wait()			#O(1)，是阻塞的，可以设置timeout超时时间。如果没有epoll_create和epoll_ctl，纯粹执行epoll_wait时，则和select和poll没什么本质区别

accept(3) = 8			#如果有客户端连接上了，返回fd = 8
cpoll_ctl(7, ADD, 8, read)	#将8同样放到内存空间7里面，继续进行epoll_wait
epoll_wait() = 8		#假设一段时间后返回8，证明有IO操作了，则需要程序自行recv，因此是同步的
recv(8)					#对8进行IO操作
```

```java
public class Test {

    private ServerSocketChannel server = null;
    private Selector selector = null; //linux 多路复用器（select poll epoll） nginx event{}
    private int port = 8080;

    public void initServer(){
        try{
            server = ServerSocketChannel.open();
            server.configureBlocking(false);
            server.bind(new InetSocketAddress(port));

            //如果在epoll模型下，open == epoll_create -> fd3
            selector = Selector.open();//优先选择epoll

            //server 约等于listen状态的 fd4
            /*
            * register
            * 如果
            * select、poll：jvm里开辟一个数组fd4放进去
            * epoll：epoll_ctl(fd3, ADD, fd4, EPOLLIN)
            */
            server.register(selector, SelectionKey.OP_ACCEPT);

        } catch (Exception e){
            e.printStackTrace();
        }
    }

    public void start(){
        initServer();
        System.out.println("服务器启动了...");
        try{
            while (true){
                Set<SelectionKey> keys = selector.keys();
                System.out.println(keys.size() + "  size");

                //1.调用多路复用器（select、poll or epoll(epoll_wait)）
                /*
                * select()是啥意思：
                * 1、select、poll 其实内核的select(fd4) poll(fd4)
                * 2、epoll：其实是内核的epoll_wait()
                * 参数可以带时间：没有时间，0：阻塞，有时间设置一个超时
                * selector.wakeup() 结果返回0
                 */
                while (selector.select(500) > 0){
                    Set<SelectionKey> selectionKeys = selector.selectedKeys(); //返回有状态的fd集合
                    Iterator<SelectionKey> iterator = selectionKeys.iterator();
                    //因此，无论是啥多路复用器，只返回状态，读取IO还是需要自己自行处理，因此是同步IO模型
                    while (iterator.hasNext()){
                        SelectionKey key = iterator.next();
                        iterator.remove(); //不移除会重复处理
                        if (key.isAcceptable()){
                            //看代码的时候，这里是重点，如果要去接受一个新的连接
                            //语义上，accept接受连接且返回新连接的fd，那新的fd怎么办
                            //select、poll：因为他们内核没有空间，那么在jvm中保存和前边的fd4那个listen的一起
                            //epoll：通过epoll_ctl把新的客户端fd注册到内核空间
                            acceptHandler(key);
                        } else if (key.isReadable()){
                            readHandler(key);
                        }
                    }
                }
            }
        } catch (Exception e){
            e.printStackTrace();
        }
    }

    private void readHandler(SelectionKey key) {
        try {
            SocketChannel channel = (SocketChannel) key.channel();
            ByteBuffer buffer = ByteBuffer.allocate(4096);
            int num = channel.read(buffer); // >0 -1 0 不会阻塞，但是经过可读校验，肯定会返回>0
            if (num > 0){
                buffer.flip();
                byte[] bytes = new byte[buffer.limit()];
                buffer.get(bytes);
                System.out.println(channel.socket().getPort() + ":" + new String(bytes));
                buffer.clear();
            }
        } catch (Exception e){
            e.printStackTrace();
        }
    }

    private void acceptHandler(SelectionKey key) {
        try{
            ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
            SocketChannel client = ssc.accept();
            client.configureBlocking(false);

            ByteBuffer buffer = ByteBuffer.allocate(8192);

            /*
             * 调用register
             * select、poll：jvm开辟数据 fd7 放进去
             * epoll：epoll_ctl(fd3, ADD, fd7, EPOLLIN)
             */
            client.register(selector, SelectionKey.OP_READ, buffer);
            System.out.println("新客户端：" + client.getRemoteAddress());

        } catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

