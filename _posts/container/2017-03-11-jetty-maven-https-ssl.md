---
layout: post
title: Quick Start - Jetty's maven plugin with SSL 
categories: Blog
description: 运用 maven 快速搭建jetty 的 HTTPS 服务。
keywords: jetty, maven, HTTPS, container
---

Speed is the key. I often need a web server in order to run a web application I developed to try things out. Setting up this infrastructure can often be quite tedious but if the only thing you need is a servlet container I often use the approach described in this article. We start out with nothing except Maven and Java installed.

Create a web application project:
```sh
$ mvn archetype:generate -DgroupId=org.example -DartifactId=example-server -DarchetypeArtifactId=maven-archetype-webapp -Dversion=1.0
```
This gives us a new directory (example-server) which is a Maven web application project. To run the web application, we configure the maven-jetty-plugin. Add the following configuration to project’s pom.xml.
```java
<build>
  <finalName>example-server</finalName>
  <plugins>
    <plugin>
      <groupId>org.mortbay.jetty</groupId>
      <artifactId>maven-jetty-plugin</artifactId>
      <version>6.1.26</version>
    </plugin>
  </plugins>
</build>
```
Enter the example-server directory and do:
```sh
$ mvn jetty:run
```
As soon as the server is started you can enter the following url in your browser.
```sh
http://localhost:8080/example-server
```
As you can see, the server is started and listens on port 8080 by default. If you want to change this, it can easily be configured. Just extend the plugin with a configuration element and add a connector.
```
<plugin>
  <groupId>org.mortbay.jetty</groupId>
  <artifactId>maven-jetty-plugin</artifactId>
  <version>6.1.26</version>
  <configuration>
    <connectors>
      <connector implementation="org.mortbay.jetty.nio.SelectChannelConnector">
        <port>9090</port>
      </connector>
    </connectors>
  </configuration>
</plugin>
```
#### Adding TLS/SSL support
Assume you want to communicate in a secure way. The only thing you need to do is to add another connector element and specify a keystore containing the server’s certificate. Simply add the following connector element and make sure the server.jks is located in your example-server directory:

```
<connector implementation="org.mortbay.jetty.security.SslSocketConnector">
  <port>9443</port>
  <keystore>${basedir}/server.jks</keystore>
  <password>password</password>
  <keyPassword>password</keyPassword>
</connector>
```
You can test this in a nice way using openssl to see what the server returns when you try to access it on port 9443.
```
$ openssl s_client -connect localhost:9443
```
Finally, if you for some reason want mutual authentication, you also need to specify a trust store in which the server keeps certificates of trusted clients. Extend the previous connector with the following information:
```
<connector implementation="org.mortbay.jetty.security.SslSocketConnector">
  <port>9443</port>
  <keystore>${basedir}/server.jks</keystore>
  <password>password</password>
  <keyPassword>password</keyPassword>
  <truststore>${basedir}/serverTruststore.jks</truststore>
  <trustPassword>password</trustPassword>
  <needClientAuth>true</needClientAuth>
</connector>
```
Now you have a web server up and running your web application with mutual authentication. The clients must provide a valid certificate in order to communicate with the server. At last I just want to add a final element to our configuration. Since TLS/SSL can be quite horrible to troubleshoot, I add the following configuration which gives a lot of nice output :)
```
<systemProperties>
  <systemProperty>
    <name>javax.net.debug</name>
    <value>ssl</value>
  </systemProperty>
<systemProperties>
```
good!


