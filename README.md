# Tinyhttpd

## 项目简介

Tinyhttpd是一个总代码量500行的超轻量型HTTP Server，使用C语言编写，有非常简单的Makefile，非常适合学习Linux网络编程的同学们拿来入门。

本项目原作者为J.David Blackstone，项目官网请[点击这里](http://sourceforge.net/projects/tinyhttpd/)。

## 组织结构说明

由于该项目是作者1999年写在Solaris系统写的，所以如果要在Linux系统上编译测试有些细微内容需要修改。

- tinyhttpd-0.1.0.tar.gz是作者写的源代码，未经任何修改的。
- Tinyhttpd For Linux是本人参考博客修改的，直接在Linux系统上make之后即可使用。

从作者源代码到Tinyhttpd For Linux需要修改的一共有以下四处。

1. `httpd.c`第33行改为`void *accept_request(void *);`，同时函数实现出也需按如下修改。

   ```c
   void *accept_request(void *);      //第33行
   void *accept_request(void* tclient)//第51行
   {
   int client = *(int *)&tclient;     //在char buf[1024];前添加该行
   ...
   return NULL;                       //第76行改为return NULL;
   ...
   return NULL;                       //在127行之后添加return NULL;
   }
   ```

2. `httpd.c`修改两个变量类型。

   ```c
   socklen_t namelen = sizeof(name);     //第438行，int改为socklen_t
   socklen_t client_name_len = sizeof(client_name); //第483行同理。
   ```

3. `httpd.c`修改一个函数参数。

   ```c
   //第497行，pthread_create函数按如下修改
   if (pthread_create(&newthread, NULL, accept_request, (void*)&client_sock) != 0)
   ```

4. `Makefile`修改编译命令。

   ```c
   //第4行按如下修改
   gcc -W -Wall -o httpd httpd.c -lpthread
   ```

> 以上修改参考博客请[点击这里](http://blog.csdn.net/cqu20093154/article/details/41025885?utm_source=blogxgwz3)。

此外，为确保运行成功，还有两个地方需要检查。

1. 确保`htdocs`文件夹内`check.cgi`和`color.cgi`具有执行权限。

   ```shell
   //使用ls -l检查二者是否具有执行权限，如果没有，执行以下命令赋予其执行权限。
   chmod +x check.cgi color.cgi
   ```

2. 检查`check.cgi`和`color.cgi`文件内容。

   ```shell
   //首先which perl得到本机perl的路径
   which perl
   //根据本机路径修改两个cgi文件开头的#!后的路径。
   ```

至此，应该可以完美运行了。