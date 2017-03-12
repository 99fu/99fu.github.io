---
layout: post
title: window 环境装ruby , jekyll
categories: Blog
description: window 下，绿色安装jekyll环境。
keywords: jekyll ,ruby ,blog
---

绿色安装jekyll环境
### 下载PortableJekyll
PortableJekyll 托管于github，地址为：[https://github.com/madhur/PortableJekyll](https://github.com/madhur/PortableJekyll/releases)

### 配制环境变量

#### How to
setpath.cmd file is a sample file, which contains all the content you want to put in the PATH environment variable.

#### No line breaks Like this:
```
x:\PortableJekyll-master\ruby\bin; x:\PortableJekyll-master\devkit\bin; x:\PortableJekyll-master\git\bin; x:\PortableJekyll-master\Python\App; x:\PortableJekyll-master\devkit\mingw\bin; x:\PortableJekyll-master\curl\bin
```
And create a new environment variable named *** SSL_CERT_FILE ***, value:
```
x:\PortableJekyll-master\curl\bin
```
The setpath.cmd use the "%~dp0" to represent your current directory, after running it in command line, the setting is ok.

Then you can run *** jekyll new myblog *** or *** jekyll serve *** anywhere you want.


