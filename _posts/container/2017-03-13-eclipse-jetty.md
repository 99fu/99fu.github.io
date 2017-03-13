---
layout: post
title: eclipse 内置 jetty 启动
categories: [eclipse ,jetty ,HTTPS]
description: eclipse 开发，不安装jetty 插件，启动jetty。
keywords: eclipse ,jetty ,HTTPS
---

## 前言
Jetty 应该是目前最活跃也是很有前景的一个 Servlet 引擎。与tomcat 相比，jetty 轻量，节约服务器性能。goole已从tomcat迁移到了jetty。
 
## http实现方式
### 1.在eclipse 中下载jetty插件

  help --> Eclipse Markeplace --> 输入jetty --> installed
  
### 2.用代码实现

 **pom**依赖中增加引用，如须新版自行修改 [阿里maven私服](http://maven.aliyun.com/nexus)。

`pom.xml`

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

`Class 启动类：`
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

## https 启动服务

先生成 keystore ，并将其存放到wepapp目录下。参考：[HTTPS 自签名配置]()

**启动代码：**
```
import org.eclipse.jetty.server.HttpConfiguration;
import org.eclipse.jetty.server.HttpConnectionFactory;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.ServerConnector;
import org.eclipse.jetty.server.SslConnectionFactory;
import org.eclipse.jetty.util.ssl.SslContextFactory;
import org.eclipse.jetty.webapp.WebAppContext;

public class StartJetty {
	private static int IDLE_THREAD_NUM = 20;
	private static int PORT = 8443;

	public static void main(String[] args) {
		startJettyByHttps();
	}

	public static void startJettyByHttps() {
		Server server = new Server();

		HttpConfiguration https_config = new HttpConfiguration();
		https_config.setSecureScheme("https");

		SslContextFactory sslContextFactory = new SslContextFactory();
		sslContextFactory.setKeyStorePath("src/main/webapp/keystore");
		// 私钥
		sslContextFactory.setKeyStorePassword("password1");
		// 公钥
		sslContextFactory.setKeyManagerPassword("password2");

		ServerConnector httpsConnector = new ServerConnector(server,
				new SslConnectionFactory(sslContextFactory, "http/1.1"), new HttpConnectionFactory(https_config));
		// 设置访问端口
		httpsConnector.setPort(PORT);
		httpsConnector.setIdleTimeout(IDLE_THREAD_NUM);
		server.addConnector(httpsConnector);

		WebAppContext webApp = new WebAppContext();
		webApp = new WebAppContext();
		webApp.setContextPath("/");
		webApp.setResourceBase("src/main/webapp");
		server.setHandler(webApp);

		try {
			server.start();
			server.join();
		} catch (Exception e) {
			e.printStackTrace();
		}

		finally {
			httpsConnector.close();
		}
	}
}

```
浏览器中访问：https://localhost:8443/





















