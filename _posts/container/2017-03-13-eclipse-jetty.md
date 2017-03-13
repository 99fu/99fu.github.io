---
layout: post
title: eclipse 内置 jetty 启动
categories: [eclipse ,jetty]
description: eclipse 开发，不安装jetty 插件，启动jetty。
keywords: eclipse ,jetty
---

## 前言：
Jetty 应该是目前最活跃也是很有前景的一个 Servlet 引擎。与tomcat 相比，jetty 轻量，节约服务器性能。goole已从tomcat迁移到了jetty。
 
## 实现
1.在eclipse 中下载jetty插件
2.用代码实现
须要在pom依赖中增加引用，如须新版自行修改 ![阿里maven私服](http://maven.aliyun.com/nexus)：
```
 <properties>
        <jetty.version>9.4.2.v20170220</jetty.version>
 </properties>
        <!-- Jetty9 -->
        <dependency>
			<groupId>org.eclipse.jetty</groupId>
			<artifactId>jetty-server</artifactId>
			<version>${jetty.version}</version>
		</dependency>
		<dependency>
			<groupId>org.eclipse.jetty</groupId>
			<artifactId>jetty-webapp</artifactId>
			<version>${jetty.version}</version>
		</dependency>
		<dependency>
			<groupId>org.eclipse.jetty</groupId>
			<artifactId>jetty-jmx</artifactId>
			<version>${jetty.version}</version>
		</dependency>
 	
```

新建启动类：
```
   
import org.eclipse.jetty.server.Connector;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.ServerConnector;
import org.eclipse.jetty.util.thread.QueuedThreadPool;
import org.eclipse.jetty.webapp.WebAppContext;

public class StartJetty {
	 private static int PORT = 8080;

	    private static int MAX_THREAD_NUM = 100;

	    private static int MIN_THREAD_NUM = 50;

	    private static int IDLE_THREAD_NUM = 20;
	   // 启动jetty https服务
	    public static void startJetty() {
	        // 实例化Server 配置线程池参数
	        Server server = new Server(new QueuedThreadPool(MAX_THREAD_NUM, MIN_THREAD_NUM, IDLE_THREAD_NUM));
	        

	        // 加载XML web配置文件
	        WebAppContext webContext = new WebAppContext();
	        server.setHandler(webContext);
	        webContext.setContextPath("/");
	        webContext.setResourceBase("src/main/webapp");
	        webContext.setClassLoader(Thread.currentThread().getContextClassLoader());

	        // 配置通信通道
	        ServerConnector connector = new ServerConnector(server);
	        connector.setPort(PORT);
	        server.setConnectors(new Connector[] { connector });

	        try {
	            server.start();
	            server.join();
	        } catch (Exception ex) {
	            ex.printStackTrace();
	        } finally {
	            // 关闭通道
	            connector.close();
	        }
	    }
	    
	    public static void main(String args[]) {
	        startJetty();
	    }
}

``` 

浏览器中访问，配置端口8080：

