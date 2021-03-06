---
title: Redis主从
date: 2018-10-17 15:58:19
tags: Redis
---

本文转载整理自http://doc.redisfans.com/topic/replication.html
Redis支持简单且易用的主从复制功能

## 1. Redis复制功能的几个重要方面
- Redis使用异步复制。从服务器会以每秒一次的频率向主服务器报告复制流的处理进度。
- 一个主服务器可以有多个从服务器。
- 从服务器也可以有多个从服务器，多个从服务器之间可以构成一个图状结构。
- 复制功能不会阻塞主服务器。即使一个或多个从服务器正在进行初次同步，主服务器也可以继续处理命令请求。
- 复制功能也不会阻塞从服务器，只要在配置文件中进行了相应的配置，即使从服务器正在进行初次同步，服务器也可以使用旧版本的数据集来处理命令查询。   
不过，在服务器删除旧版本数据集并载入新版本数据集的时间间隔内，连接请求会被阻塞。
还可以配置从服务器，让它在与主服务器的连接断开时，向客户端发送一个错误。
- 复制功能可以单纯地用于数据冗余，也可以通过让多个从服务器处理只读命令来提升扩展性：比如繁重的`SORT`命令可以交给附属节点去运行。
- 可以通过复制功能来让主服务器免于执行持久化操作：只要关闭主服务器的持久化功能，然后由从服务器去执行持久化操作即可。

## 2.复制功能的原理

1. 无论是初次连接还是重新连接，当建立一个从服务器时，从服务器都将向主服务器发送一个`SYNC`命令。
2. 接到`SYNC`命令的主服务器将开始执行`BGSAVE`，并在保存操作执行期间，将所有新执行的写入命令都保存到一个缓冲区里。
3. 当`BGSAVE`执行完毕后，主服务器将执行保存操作所得的`.rdb`文件发送给从服务器，从服务器接收到`.rdb`文件后，将文件中的数据载入到内存中。
4. 之后，主服务器会以Redis命令协议的格式，将写命令缓冲区中积累的所有内容都发送给从服务器。

即使有多个从服务器同时向主服务器发送`SYNC`，主服务器也只需要执行一次`BGSAVE`命令就可以处理所有这些从服务器的同步请求。

从服务器可以在主从服务器之间的连接断开时进行自动重连  
在Redis 2.8之前，断线重连的从服务器总要执行一次完整重同步操作。  
在Redis 2.8之后，从服务器可以根据主服务器的情况来选择执行完整重同步还是部分重同步。

## 3. 部分重同步
从Redis 2.8开始，在网络连接短暂断开后，主从服务器可以尝试继续执行原有的复制进程，而不一定要执行完整重同步操作。

这个特性需要主服务器为被发送的复制流创建一个内存缓冲区，并且主服务器和所有从服务器之间都记录一个复制偏移量和一个主服务器ID，当出现网络断开时，从服务器将重新连接，并且向主服务器请求继续执行原来的复制进程。

- 如果从服务器记录的主服务器ID和当前要连接的主服务器的ID相同，并且从服务器记录的偏移量所指定的数据仍然保存在主服务器的复制流缓冲区里，那么主服务器会向从服务器发送断线时缺失的那部分数据，然后复制工作可以继续进行。
- 否则。从服务器要执行完整重同步操作。

Redis的部分重同步特性会用到一个新增的`PSYNC`内部命令，而Redis 2.8之前的旧版本只有`SYNC`命令，不过只要从服务器是Redis 2.8或者以上的版本，它会根据主服务器的版本来决定到底是使用`PSYNC`还是`SYNC`

- 主服务器 >= 2.8 从服务器使用`PSYNC`命令来进行同步。 
- 主服务器 < 2.8 从服务器使用`SYNC`命令来进行同步。 

## 4. 配置

配置一个从服务器非常简单，只要在配置文件中增加以下一行就可以
```
slaveof 192.168.1.100 6379
```
当然，你需要将代码中的`192.168.1.100` 和`6379`替换成你的主服务器的IP和端口号。  
另外一种方法是调用`SLAVEOF`命令，输入主服务器的IP和端口号，然后同步就会开始：
```
127.0.0.1:6379> SLAVEOF 192.168.1.100 6379
OK
```
在从服务器上登录Redis客户端查询，可以看到数据已经从主服务器同步过来。

## 5. 只读从服务器
从Redis 2.6开始，从服务器支持只读模式，并且该模式为从服务器的默认模式。

只读模式由配置文件中的 `slave-read-only`选项控制，也可以通过`CONFIG SET`命令来开启或者关闭这个模式。

只读从服务器会拒绝执行任何写命令，所以不会出现因为操作失误而将数据不小心写到了从服务器的情况。

即使从服务器是只读的，`DEBUG`和`CONFIG` 等管理命令仍然是可以使用的，所以我们还是不应该将服务器暴露给互联网或者任何不可信的网络。不过，使用配置中的命令改名选项，我们可以通过禁止执行某些命令来提升只读从服务器的安全性。

既然从服务器上的写数据会被重同步数据覆盖，也可能在服务器重启时丢失，为什么要让一个从服务器变得可写呢？
原因是，一些不重要的临时数据，仍然是可以保存在从服务器上的。

## 6. 从服务器的相关配置
如果主服务器通过`requirepass`选项设置了密码，那么为了让从服务器的同步操作可以顺利进行，我们也必须为从服务器进行相应的身份验证设置。

对于一个正在运行的服务器，可以使用客户端输入以下命令
```
127.0.0.1:6379> config set masterauth <password>
```
要永久地设置这个密码，那么可以将它加入到配置文件中：
``` 
masterauth <password>
```

## 7. 主服务器只在有至少N个从服务器的情况下，才执行写操作

从Redis 2.8开始，为了保证数据的安全性，可以通过配置，让主服务器只在有至少N个当前已连接从服务器的情况下，才执行写命令。
不过，因为Redis使用异步复制，所以主服务器发送的写数据并不一定会被从服务器收到，因此，数据丢失的可能性仍然是存在的。

以下是这个特性的原理：
- 从服务器以每秒一次的频率`ping`主服务器一次，并报告复制流的处理情况。
- 主服务器会记录各个从服务器最后一次向它发送`ping`的时间。
- 用户可以通过配置，指定网络延迟的最大值`min-slaves-max-lag`，以及执行写操作所需的至少从服务器数量`min-slaves-to-write`.

如果至少有`min-slaves-to-write`个服务器，并且这些服务器的延迟都少于`min-slaves-max-lag`秒，那么主服务器就会执行客户端请求的写操作。  
尽管不能保证写操作的持久性，但起码丢失数据的窗口会被严格限制的指定的秒数中。  
另一方面，如果条件达不到`min-slaves-to-write`和`min-slaves-max-lag`所指定的条件，那么写操作就不会被执行，主服务器会向请求执行写操作的客户端返回一个错误。  
以下是这个特性的两个选项和它们所需的参数：
- `min-slaves-to-write <number of slaves>`
- `min-slaves-max-lag <number of second>`


