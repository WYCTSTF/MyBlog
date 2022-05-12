---
title: Java基础知识笔记
categories: Note
---

gwj讲的课不是很理想 还随手扯js MVC..

先把理念搞搞懂好吧.. 布置的作业还是可以的 java听课的时间真不如拿来打炉石

前面没什么困难 到了多线程这里开始有问题

网上搜了点东西 要做笔记 说是笔记 其实也就是把网上的资料抄抄到一起 首选当然还是我的最爱[Runoob](https://www.runoob.com/)

<!-- more -->

----

****前置的一些非常阉割的内容可以看[OI-wiki](https://oi-wiki.org/lang/java/)**

## 杂项

* Java中的static表示静态或全局的意思，用来修饰成员变量和成员方法，即不依赖类特定的实例，被类的所有实例共享
* Java中的var类似C++ auto 在编译期的自动类推导
* Java中的Final关键字
  * 修饰类的时候 表示这个类不能被继承 final类中的所有方法都会被隐式的指定为final方法
  * 修饰方法 将方法锁定 以防任何子类修改它的含义 类中的private都会被隐式的指定为final
  * 修饰变量
    * 如果是基本数据类型 数值在初始化之后就不能更改
    * 如果是引用类型 在初始化之后就不能指向另一个对象

## IO stream、File

主要是FileWriter FileReader FileInputStream FileOuputStream这四个内置类

具体用法百度一下就好了

还有一些其他的输入 输出流:

[ByteArrayInputStream](https://www.runoob.com/java/java-bytearrayinputstream.html) [ByteArrayOuputStream](https://www.runoob.com/java/java-bytearrayoutputstream.html) [DataInputStream](https://www.runoob.com/java/java-datainputstream.html) [DataOutputStream](https://www.runoob.com/java/java-dataoutputstream.html)



### 作业一： 向文件内头部添加内容

FileWriter只能在文件末尾添加

所以一种憨憨思路是复制原文件内容 清空原来的 插入新的之后 再把原来内容插入进去

{% fold code %}
```java
import java.io.*;
import java.util.*;
public class fwrite {
	public static void main(String[] args) {
		Scanner scan = new Scanner(System.in);
		System.out.println("输入要修改的文件名 时间 操作人名称");
		String fileName = scan.nextLine();
		String date = scan.nextLine();
		String PersonName = scan.nextLine();
		Boolean tag = false;
		try {
			File file = new File(fileName);
			if (file.createNewFile()) System.out.println("文件创建成功");
			else { System.out.println("文件已存在"); tag = true; }
		} catch (IOException e) {
			e.printStackTrace();
		}
		File tem = new File("temporayryRecord");
		char[] buf = new char[5010];
		try {
			if (tag) {
				tem.createNewFile();
				FileReader fr = new FileReader(fileName);
				fr.read(buf);
			}
			FileWriter fw = new FileWriter(fileName, false);
			fw.write("Time: "+date+"\nAuthor: "+PersonName+"\n");
			if (tag) {
				for (char c : buf) {
					if (c == '\0') break;
					fw.append(c);
				}
				tem.delete();
			}
			fw.close(); scan.close();
		}
		catch (IOException e) {
			System.out.println("写入失败"); e.printStackTrace();
		}
	}
}
```
{% endfold %}

### 作业二：命令行参数给定源文件 新文件名称 要求复制源文件图片到新文件

用流去存内容 图片文件也支持

{% fold code %}
```java
import java.io.*;

public class MyCopy {
	public static void main(String[] args) {
		String src = args[0];
		String target = args[1];

		File srcFile = new File(src);
		File targetFile = new File(target);
		
		try {
			InputStream is = new FileInputStream(srcFile);
			OutputStream os = new FileOutputStream(targetFile);
			byte[] bytes = new byte[1024];
			int len = -1;
			while ((len = is.read(bytes)) != -1) {
				os.write(bytes, 0, len);
			}
			is.close();
			os.close();
		} catch (FileNotFoundException e) {
			System.out.println("文件未找到");
			e.printStackTrace();
		} catch (IOException e) {
			System.out.println("读写失败");
			e.printStackTrace();
		}
	}
}
```
{% endfold %}

## 多线程

一个线程有5个状态 这种说法不对 参考[知乎问答](https://www.zhihu.com/question/56494969)

5种是早期进程的状态 混淆了进程和线程的概念（我是懒狗，具体概念不查了）

--- 

多线程程序在较低的层次上扩展了多任务的概念 一个程序同时执行多个任务

通常，每一个任务称为一个线程(thread) 它是线程控制的简称 可以同时运行一个线程以上的程序称为多线程程序

多进程与多线程的区别：每个进程拥有自己的一套变量，而线程则共享数据

有这些基础再去看[Runoob的内容](https://www.runoob.com/java/java-multithreading.html)就不容易混淆一点

线程是随机执行的 比方说下面的例子

{% fold code %}
```java
public class trial {
	static Thread thread1 = new Thread(new Runnable() {
		@Override
		public void run() { System.out.println("thread1");}
	});
	static Thread thread2 = new Thread(new Runnable() {
		@Override
		public void run() { System.out.println("thread2");}
	});
	static Thread thread3 = new Thread(new Runnable() {
		@Override
		public void run() { System.out.println("thread3");}
	});
	static Thread thread4 = new Thread(new Runnable() {
		@Override
		public void run() { System.out.println("thread4");}
	});
	static Thread thread5 = new Thread(new Runnable() {
		@Override
		public void run() { System.out.println("thread5");}
	});
	public static void main(String[] args) {
		thread1.start();
		thread2.start();
		thread3.start();
		thread4.start();
		thread5.start();
	}
}
```
{% endfold %}

调用join方法可以保证顺序 具体参考[这篇博客](https://blog.csdn.net/NTSDB/article/details/104442527)

{% fold code %}
```java
public static void main(String[] args) throws Exception {
		t1.start();
		t1.join();
		t2.start();
		t2.join();
	}
```
{% endfold %}

实现Runnable的方式直接用join() 好像就不行 也可能是有啥我不知道的方法

下面这个加了join还是没啥用

{% fold code %}
```java
/* 
 * 三种创建线程的方法：
 * 1. 通过实现Runnable(内置)接口
 * 2. 通过继承Thread类本身
 * 3. 通过Callable和Future创建线程
 */

// 1. 通过实现Runnable接口
// 为了实现Runnable，一个类只需要执行一个方法调用run()
class RunnableDemo implements Runnable {

	// 在创建了一个实现Runnable接口的类之后，可以在类中实例化一个线程对象
	// 常用的Thread构造方法是
	// Thread(Runnable threadOb,String threadName);
	private Thread t;
	private String threadName;

	RunnableDemo(String name) {
		threadName = name;
		System.out.println("Creating " + threadName);
	}

	public void run() {
		System.out.println("Running "+ threadName);
		try {
			for (int i = 4; i > 0; i--) {
				System.out.println("Thread: "+ threadName + ", " + i);
				Thread.sleep(50);
			}
		} catch(InterruptedException e) {
			System.out.println("Thread" + threadName + " interrupted.");
		}
		System.out.println("Thread " + threadName + " exiting.");
	}

	public void start() {
		System.out.println("Starting " + threadName);
		if (t == null) {
			t = new Thread (this, threadName);
			t.start();
		}
	}
}

// 2. 继承Thread来创建线程
// 创建一个新的类 继承Thread类 然后创建一个该类的实例
// 继承类必须重写run()方法 该方法是新线程的入口点 也必须调用start()才能执行
// 本质上也是实现了Runnable接口的一个实例

class ThreadDemo extends Thread {
	private Thread t;
	private String threadName;
	
	ThreadDemo(String name) {
		threadName = name;
		// System.out.println("Creating " + threadName);
	}

	public void run() {
		System.out.println("Running "+ threadName);
		try {
			for (int i = 4; i > 0; i--) {
				// if (t.isAlive()) {
				// 	System.out.println("YEP");
				// }
				System.out.println("Thread: " + threadName + ", " + i);
				// Thread.sleep(50);
			}
		} finally {
		}
		System.out.println("Thread " + threadName + " exiting.");
	}

	public void start() {
		// System.out.println("Starting " + threadName);
		if (t == null) {
			t = new Thread(this, threadName);
			t.start();
		}
	}
}

public class TestThread {
	public static void main(String[] args) throws Exception {
/* 		// 方式1
		RunnableDemo R1 = new RunnableDemo("Thread-1");
		R1.start();
		Thread.sleep(200);
		RunnableDemo R2 = new RunnableDemo("Thread-2");
		R2.start(); */
		ThreadDemo T1 = new ThreadDemo("Thread-1");
		T1.start();
		T1.join();
		// if (T1.isAlive()) {
		// 	System.out.println("YES");
		// } else {
		// 	System.out.println("NO");
		// }
		ThreadDemo T2 = new ThreadDemo("Thread-2");
		T2.start();
		T2.join();
	}
}
```
{% endfold %}

这种多线程随机的执行顺序会使得共享数据操作的时候出问题

{% fold code %}
```java
public class ticket_plus {
	public static void main(String[] args) throws Exception {
		var add = new AddThread();
		var dec = new DecThread();
		add.start();
		dec.start();
		add.join();
		dec.join();
		System.out.println(Counter.count);
	}
}
class Counter {
	public static int count = 0;
}
class AddThread extends Thread {
	public void run() {
		for (int i = 0; i < 10000; i++) { Counter.count++; }
	}
}
class DecThread extends Thread {
	public void run() {
		for (int i = 0; i < 10000; i++) { Counter.count--; }
	}
}
```
{% endfold %}

像这里最后的输出结果就不是0 而是一个随机的值

具体内容看[廖雪峰的java教程部分](https://www.liaoxuefeng.com/wiki/1252599548343744/1306580844806178) 啥原子操作的 有机会把CSAPP看一遍吧(我虽然对底层很感兴趣，但是我懒狗一条..)

关键一点是例如n=n+1这一条语句的过程中 并不是一次操作 而是涉及三条指令

然后线程在一次执行的时候 可能一个线程的指令1刚刚执行完 后来的线程又打断了这个过程 因此前面的操作并不连续

因此要给存储公共数据的对象添加加锁解锁的操作 使得三条指令在一个线程上做完 再进行下一次操作

{% fold code %}
```java
import java.util.Scanner;
public class ticket {
	public static void main(String[] args) {
		Scanner scan = new Scanner(System.in);
		System.out.println("输入总票数M");
		Control.total = scan.nextInt();
		Thread t1 = new Thread(new RunnableDemo("窗口1"));
		Thread t2 = new Thread(new RunnableDemo("窗口2"));
		t1.start();
		t2.start();
		scan.close();
	}
}
class Control {
	public static final Object lock = new Object();
	public static int total;
}
class RunnableDemo implements Runnable {
	private Thread t;
	private String threadName;
	RunnableDemo(String name) {
		threadName = name;
	}
	public void run() {
		while (true) {
			try { Thread.sleep(20); }
			catch(InterruptedException e) { e.printStackTrace(); }
			synchronized(Control.lock) {
				if (Control.total > 0) {
					Control.total--;
					System.out.println(threadName + "售票1张");
				} else {
					System.out.println("票已售完");
					System.exit(0);
				}
			}
		}
	}
}
```
{% endfold %}

多线程的内容很多很多 即使是基础我也没有学完皮毛 只能是先应付作业 之后再回来补上

## 网络编程

看着UDP TCP/IP啥的 突然想起之前准备初赛的时候 去考场的大巴上在临时背那7层 5层东西 xy当时被我吵得自闭了hh

java.net包提供对以上两种协议的支持

网络编程分两种 Socket编程和URL处理

这部分的内容看 [Runoob](https://www.runoob.com/java/java-networking.html) 基本没有问题

所谓套接字(Socket)，就是对网络中不同主机上的应用进程之间进行双向通信的端点的抽象。

说句废话，服务器和客户端依赖网络实现数据的传输 那么我们需要有对应的服务器和客户端的类以及方法

### ServerSocket类

服务器应用程序通过使用 java.net.ServerSocket 类以获取一个端口,并且侦听客户端请求。

* public ServerSocket(int port) throws IOException
  * 创建绑定到特定端口的服务器套接字。
* public ServerSocket(int port, int backlog) throws IOException
  * 利用指定的 backlog 创建服务器套接字并将其绑定到指定的本地端口号。
* public ServerSocket(int port, int backlog, InetAddress address) throws IOException
  * 使用指定的端口、侦听 backlog 和要绑定到的本地 IP 地址创建服务器。
* public ServerSocket() throws IOException
  * 创建非绑定服务器套接字。

这四个构造方法实现一个端口

ServerSocket 类有几个常用的方法

* public int getLocalPort()
	* 返回此套接字在其上侦听的端口。
* public Socket accept() throws IOException
	* 侦听并接受到此套接字的连接。
* public void setSoTimeout(int timeout)
  * 通过指定超时值启用/禁用 SO_TIMEOUT，以毫秒为单位。
* public void bind(SocketAddress host, int backlog)
  * 将 ServerSocket 绑定到特定地址（IP 地址和端口号）。

### Socket类

每个 socket 都有一个输入\输出流 **客户端的输入流和服务端的输出流绑定 反之相同**

java.net.Socket 类代表客户端和服务器都用来互相沟通的套接字。客户端要获取一个 Socket 对象通过实例化 ，而 服务器获得一个 Socket 对象则通过 accept() 方法的返回值。 

Socket 类有五个构造方法.

* public Socket(String host, int port) throws UnknownHostException, IOException.
  * 创建一个流套接字并将其连接到指定主机上的指定端口号。
* public Socket(InetAddress host, int port) throws IOException
  * 创建一个流套接字并将其连接到指定 IP 地址的指定端口号。
* public Socket(String host, int port, InetAddress localAddress, int localPort) throws IOException.
  * 创建一个套接字并将其连接到指定远程主机上的指定远程端口。
* public Socket(InetAddress host, int port, InetAddress localAddress, int localPort) throws IOException.
  * 创建一个套接字并将其连接到指定远程地址上的指定远程端口。
* public Socket()
  * 通过系统默认类型的 SocketImpl 创建未连接套接字

当 Socket 构造方法返回，并没有简单的实例化了一个 Socket 对象，它实际上会尝试连接到指定的服务器和端口。

### 大体流程

搜到[这篇blog](https://blog.csdn.net/a78270528/article/details/80318571) 讲的还可以

#### 服务器端步骤
1. 创建ServerSocket对象，绑定监听端口
2. 通过accept()方法监听客户端请求
3. 连接建立后，通过输入流读取客户端发送的请求信息
4. 通过输出流向客户端发送响应信息
5. 关闭相应资源

#### 客户端步骤

1. 创建Socket对象，指定要连接的服务器的地址和端口号
2. 连接建立后，通过输出流向服务器发送请求信息
3. 通过输入流获取服务器响应的信息
4. 关闭相应资源

#### 最基本的模型

服务端

{% fold code %}
```java
import java.net.*;
import java.io.*;

public class Server {
	public static void main(String[] args) throws Exception {
		// 指定监听端口
		int port = 4001;
		// 如果从命令行参数 传入指定端口
		port = Integer.parseInt(args[0]);
		ServerSocket server = new ServerSocket(port);
		System.out.println("服务器等待客户端连接..");
		// accept()产生一个阻塞 直到接受一个连接 并且返回一个客户端的Socket实例
		Socket socket = server.accept();
		// 得到当前socket的输入流 连接着客户端的输出流
		InputStream is = socket.getInputStream();
		byte[] bytes = new byte[1024];
		int len;
		StringBuilder sb = new StringBuilder();
		while ((len = is.read(bytes)) != -1) {
			// 注意指定格式编码 发送方和接收方一定要统一
			sb.append(new String(bytes, 0, len, "UTF-8"));
		}
		System.out.println("get message from client - " + sb);
		is.close();
		socket.close();
		server.close();
	}
}
```
{% endfold %}

客户端

{% fold code %}
```java
import java.io.OutputStream;
import java.net.Socket;

public class FileClient {
	public static void main(String[] args) throws Exception {
		// 指定IP地址
		String host = "127.0.0.1";
		// 从命令行参数得到IP地址
		host = args[0];
		// 指定端口
		int port = 4001;
		// 从命令行获取
		port = Integer.parseInt(args[1]);
		// 与服务器建立连接
		Socket socket = new socket(host, port);
		// 得到客户端输出流 与服务端输入流关联
		OutputStream os = socket.getOutputStream();
		String message = "你好 小帅哥";
		socket.getOutputStream().write(message.getBytes("UTF-8"));
		os.close();
		socket.close();
	}
}
```
{% endfold %}

这个例子比较基础 适合熟悉概念

#### 多线程实现服务器和多客户端之间通信步骤

1. 服务端创建ServerSocket，循环调用accept()等待客户端连接
2. 客户端创建一个socket并请求和服务器端连接
3. 服务器端接收客户端请求，创建socket与该客户端建立专线连接
4. 建立连接的两个socket在一个单独的线程上开始对话
5. 服务器端继续等待新的连接

#### 作业1 要求是写一个客户端输入n 服务器计算并返回n！的值

{% fold 服务端 %}
```java
import java.net.*;
import java.io.*;

public class Server extends Thread {
	private ServerSocket ss;

	public Server(int port) throws IOException {
		ss = new ServerSocket(port);
		ss.setSoTimeout(20000);
	}

	public void run() {
		while (true) {
			try {
				Socket socket = ss.accept();
				System.out.println("连接到远程主机 - " + socket.getRemoteSocketAddress());
				InputStream is = socket.getInputStream();
				OutputStream os = socket.getOutputStream();
				int n = is.read(), tem = 1, ans = 0;
				for (int i = 1; i <= n; i++) { tem *= i; ans += tem; }
				os.write(ans); os.flush();
				is.close();
				os.close();
				socket.close();
			} catch (SocketTimeoutException s) {
				System.out.println("服务器等待响应超时 已退出");
				break;
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}

	public static void main(String[] args) {
		int port = Integer.parseInt(args[0]);
		try {
			Thread t = new Server(port);
			t.start();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```
{% endfold %}

{% fold 客户端 %}
```java
import java.io.*;
import java.net.*;
import java.util.*;

public class Client {
	public static void main(String[] args) {
		String serverName = args[0];
		int port = Integer.parseInt(args[1]);
		try {
			Scanner scan = new Scanner(System.in);
			int n = scan.nextInt();
			Socket socket = new Socket(serverName, port);
			// System.out.println("远程主机地址 - " + socket.getRemoteSocketAddress());
			OutputStream os = socket.getOutputStream();
			os.write(n); os.flush();
			InputStream is = socket.getInputStream();
			int ans = is.read();
			System.out.print(ans);
			socket.close();
			scan.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```
{% endfold %}

网络编程的第二个作业要求：

Server端要能够接受多客户端同时访问请求

Client客户端可以向服务器发送请求文件(包括文件路径和文件名)，请求文件存在的话下载到本地 若不存在提示重新输入文件或者输入bye退出程序

把几个常用的东西缝合了一下 只要找一个多线程实现多客户端的简单例子 这个作业就能很轻易的写掉

百度了一个线程池的模板直接偷懒了 别的内容比较憨憨 不细嗦了..

{% fold 服务端 %}
```java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.EOFException;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
public class FileServer {
	public static void main(String[] args) throws Exception {
		int port = 4001;
		ServerSocket ss=new ServerSocket(port);
		System.out.println("服务器等待连接...");
		ExecutorService threadPool=Executors.newFixedThreadPool(100);
		while (true) {
			Socket socket=ss.accept();
			System.out.println("连接到远程主机 - "+socket.getRemoteSocketAddress());
			Runnable runnable=()->{
				try {
					DataInputStream dis=new DataInputStream(socket.getInputStream());
					DataOutputStream dos=new DataOutputStream(socket.getOutputStream());
					int len;
					byte[] bytes=new byte[1024];
					String filePath=dis.readUTF();
					try {
						File src=new File(filePath);
						FileInputStream fis=new FileInputStream(src);
						dos.writeUTF("success"); dos.flush();
						while ((len=fis.read(bytes)) != -1) {
							dos.write(bytes, 0, len);
						}
						dos.flush();
						fis.close();
						System.out.println("文件传输结束");
						socket.close();
					} catch (FileNotFoundException fnf) {
						dos.writeUTF("fail"); dos.flush();
					}
				} catch (EOFException e) {
					System.out.println("客户端"+socket.getRemoteSocketAddress()+"终止");
				} catch (Exception e) {
					e.printStackTrace();
				} 
			};
			threadPool.submit(runnable);
		}
	}
}
```
{% endfold %}

{% fold 客户端 %}
```java
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.net.Socket;
import java.util.Scanner;

public class FileClient {
	public static void main(String[] args) throws Exception {
		String host="localhost";
		int port=4001;
		Scanner scan=new Scanner(System.in);
		while (true) {
			int len;
			byte[] bytes=new byte[1024];
			Socket socket=new Socket(host, port);
			// assert connection
			System.out.println("已连接到服务器，本机地址 - "+socket.getLocalSocketAddress());
			System.out.println("请输入文件路径 或者 输入 bye 以退出");
			String filePath=scan.next();
			DataInputStream dis=new DataInputStream(socket.getInputStream());
			DataOutputStream dos=new DataOutputStream(socket.getOutputStream());
			if (filePath.equals("bye")) {
				System.out.println("拜拜");
				break;
			}
			dos.writeUTF(filePath); dos.flush();
			String message=dis.readUTF();
			if (message.equals("success")) {
				System.out.println("文件已找到 正在传输至本地");
				String[] src=filePath.split("/");
				String target=src[src.length-1];
				File tar=new File(target);
				tar.createNewFile();
				OutputStream os=new FileOutputStream(tar);
				while ((len=dis.read(bytes))!=-1){
					os.write(bytes,0,len);
					os.flush();
				}
				os.close();
				System.out.println("传输完成");
				break;
			} else {
				System.out.println("文件不存在 请重试");
			}
		}
		scan.close();
	}
}
```
{% endfold %}

自己写完这个之后总算是解决了一些困惑[虽然这么傻逼也写了很久]

## jdbc | MySQL笔记

看到作业9 我是真心没绷住

百度，都可以百度

<!-- ![](../images/winwin.jpg) -->

作业9：

请使用Java的jdbc技术开发一个班级管理系统
1. 实现班级成员的信息管理（添加成员、删除成员、修改成员信息、查找成员信息）
2. 数据库使用Mysql存储信息
3. 成员信息包括学号、姓名、性别、年龄、联系电话、宿舍编号（楼宇、楼层、房间号）、籍贯、家庭住址
4. 班委可以执行所有操作，其他成员只能修改自己的信息（学号不可以修改）
5. 本系统基于GUI（可使用Swing或者JFX技术

额 就他这个什么实际内容都不教的行为来看 酱紫布置作业确实挺逆天的

## 一些吐槽

GWJ：百度 都可以百度

然而事实上是网络上确实有很多好的资料，也都集成好了 那么，要设置这门课程有什么用呢？

大部分人进学校之前没有编程基础，大一上的时候学C，对各种类型、函数等基础概念以及面向过程有了一定概念

然后按照教学计划突突突刷掉了C++，说白了也就是个C with classes，教材用的是黑马的那本..

照着书上的内容，把10%不到的实例代码抄了一下，那么假定许多人在没学会的情况下，课后自己好好补上了，然后开始这个学期的Java，前面慢吞吞面向对象，后面一节课异常、一节课IO流、一节课线程、一节课网络编程、一节课序列化

一节课2个小时的时间里面还要抽出大部分时间扯淡，真的很难绷住

---

## 参考资料
* Java核心技术 卷1 基础知识 原书第九版

* [Runoob菜鸟教程](https://www.runoob.com/)
* [廖雪峰的java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)**