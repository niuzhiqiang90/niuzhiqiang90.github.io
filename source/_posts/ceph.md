---
title: ceph
date: 2017-03-13 21:13:03
categories: ceph
tags: ceph
---
# ceph的搭建

**注** 如果在安装过程中，想要重新安装，需要执行如下命令清除
```
sudo stop ceph-all  #停止所有CEPH进程
ceph-deploy uninstall  [{ceph-node}]   #卸载所有ceph程序
ceph-deploy purge   [[ceph-node} [{ceph-node}] #删除ceph相关的包
ceph-deploy purgedata {ceph-node} [{ceph-node}]   #删除ceph相关的包
ceph-deploy forgetkeys #删除key  
```

## 1. 安装前准备
### 1.1 添加hosts解析
```
sudo vi /etc/hosts

# 添加如下内容
192.168.1.174	node1
192.168.1.175	node2
```
**注:  <font color=red>node1 node2必须为hostname，因为在安装过程中会验证hostname是否为node{1,2}
遇到这个错误，需要完全清理ceph软件和配置数据，从头再来
</font>**


### 1.2 免密码登录
```
# 生成密钥对
ssh-keygen -t rsa

# admin节点需要通过密钥对登录到其他节点
ssh-copy-id ceph@node1
ssh-copy-id ceph@node2
```

在**每个Ceph节点**中为用户增加 root 权限
```
echo "ceph ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ceph
sudo chmod 0440 /etc/sudoers.d/ceph
```

### 1.3 安装ceph-deploy软件

```
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -

# 添加Ceph软件包源，用Ceph稳定版（如cuttlefish 、 dumpling 、 emperor 、 firefly 等等）替换掉 {ceph-stable-release}
# echo deb http://download.ceph.com/debian-{ceph-stable-release}/ $(lsb_release -sc) main | sudo tee /etc/apt/sources.list.d/ceph.list
# 例如：
echo deb http://download.ceph.com/debian-dumpling/ $(lsb_release -sc) main | sudo tee /etc/apt/sources.list.d/ceph.list
sudo apt-get update
sudo apt-get install ceph-deploy
```

## 2. 创建ceph目录

我们使用ceph-deploy部署，**后续操作均在admin节点操作**。
首先需要创建工作环境，该环境会保存所有的配置文件：
```
mkdir opt/ceph
cd opt/ceph
```

## 3. 创建集群

```
ceph@node1:~/opt/ceph$ ceph-deploy new node1
```
其中node1是mon节点，执行该命令会生成ceph配置文件、monitor密钥文件以及日志文件。
```
ceph@node1:~/opt/ceph$ ls
ceph.conf  ceph.log  ceph.mon.keyring
```

## 4. 修改默认冗余份数

### 4.1 设置osd冗余份数
由于我们目前只有两个osd节点，而默认的冗余份数是3，因此我们需要设置为2，如果osd节点大于2，则此步骤省略。
修改ceph.conf文件，在[global]下增加以下内容：
```
ceph@node1:~/opt/ceph$ echo "osd pool default size = 2" >> ceph.conf
```
**如果有多块网卡需要配置使用哪块网卡**
```
mon_host = `对应网卡的IP地址`
```

### 4.2 在节点上安装ceph软件
```
ceph@node1:~/opt/ceph$ ceph-deploy install node1 node2
```
如果失败会报如下错误
```
[node1][ERROR ] RuntimeError: command returned non-zero exit status: 100
[ceph_deploy][ERROR ] RuntimeError: Failed to execute command: apt-get -q update
```
**解决方法**
- 1.同步配置
```
ceph@node1:~/opt/ceph$ rsync -avz ../ceph ceph@node2:~/opt/
```
- 2.在<font color=red>所有节点</font>分别安装ceph
```
ceph@node1:~/opt/ceph$ sudo apt-get install ceph
```

## 5. 初始化mon节点和收集密钥信息
```
ceph@node1:~/opt/ceph$ ceph-deploy mon create-initial
```
如果报如下错误
```
[node1][DEBUG ] get remote short hostname
[node1][WARNIN] ********************************************************************************
[node1][WARNIN] provided hostname must match remote hostname
[node1][WARNIN] provided hostname: node1
[node1][WARNIN] remote hostname: ubuntu
[node1][WARNIN] monitors may not reach quorum and create-keys will not complete
```
因为/etc/hosts中解析的`ip地址 主机名`和远端的主机名不匹配所致，在/etc/hosts中修改为正确的hostname，清理下从头重新安装。

执行完毕，目录应该有以下文件：
```
{cluster-name}.client.admin.keyring
{cluster-name}.bootstrap-osd.keyring
{cluster-name}.bootstrap-mds.keyring
{cluster-name}.bootstrap-rgw.keyring
```

## 6. 配置osd节点
首先格式化磁盘，注意我们使用的是/dev/sdb：
```
ceph@node1:~/opt/ceph$ ceph-deploy disk zap node1:sdb
ceph@node1:~/opt/ceph$ ceph-deploy disk zap node2:sdb
```
以上步骤会<font color=red>清空磁盘的所有数据</font>。

接下来创建osd，注意由于我们只是测试，故没有使用单独的磁盘作为journal
(todo分析具体什么)
实际在生产环境下，需要配备SSD分区作为journal，能够最大化IO吞吐量。
```
ceph@node1:~/opt/ceph$ ceph-deploy osd create node1:sdb
ceph@node1:~/opt/ceph$ ceph-deploy osd create node2:sdb
```
在节点上执行`df -h`会得到如下结果
```
/dev/sdb1        35G   36M   35G   1% /var/lib/ceph/osd/ceph-0
```


## 7. 配置admin节点
admin节点同时也作为我们的client节点，需要拷贝其他节点的配置文件以及密钥，使得不需要指定mon地址以及用户信息就可以直接管理我们的ceph集群，执行以下命令即可：
```
ceph@node1:~/opt/ceph$ ceph-deploy admin node1 node2
ceph@node1:~/opt/ceph$ sudo chmod +r /etc/ceph/ceph.client.admin.keyring # 保证具有读取的密钥的权限
```

## 8. 测试结果

运行以下命令：
```
ceph@node1:~/opt/ceph$ ceph health
ceph@node1:~/opt/ceph$ ceph -s
```
结果若返回active + clean状态，则说明部署成功！


## 9. 配置mds
[Adding MDS参考链接](http://docs.ceph.com/docs/master/install/manual-deployment/#adding-mds)
### 9.1 安装mds
```
ceph@node1:~/opt/ceph$ sudo apt-get install ceph-mds
```

### 9.2 配置
{id}是一个任意的名字,例如可以是电脑的主机名

编辑`ceph.conf`，添加MDS区域
```
[mds]
  mds data = /var/lib/ceph/mds/mds.$id
  keyring = /var/lib/ceph/mds/mds.$id/mds.$id.keyring

[mds.0] # 0为id
  host = 0 # {id}
```

### 9.3 创建目录
```
ceph@node1:~/opt/ceph$ mkdir -p /var/lib/ceph/mds/{cluster-name}-{id}
```

### 9.4 生成keyring
```
ceph@node1:~/opt/ceph$ ceph-authtool --create-keyring /var/lib/ceph/mds/{cluster-name}-{id}/keyring --gen-key -n mds.{id}
```

Import the keyring and set caps.:

### 9.5 导入keyring
```
ceph@node1:~/opt/ceph$ ceph auth add mds.{id} osd "allow rwx" mds "allow" mon "allow profile mds" -i /var/lib/ceph/mds/{cluster}-{id}/keyring
```
### 9.6 修改配置文件
Add to ceph.conf.:
```
[mds.{id}]
host = {id}
```
### 9.7 手动启动
Start the daemon the manual way.:
```
ceph@node1:~/opt/ceph$ ceph-mds --cluster {cluster-name} -i {id} -m {mon-hostname}:{mon-port} [-f]
```
```
ceph@node1:~/opt/ceph$ service ceph start
ceph@node1:~/opt/ceph$ sudo service ceph start mds
```
检查进程是否存在
```
ceph@node1:~/opt/ceph$ ps -ef | grep ceph
root      4032     1  0 17:18 ?        00:00:00 /usr/bin/ceph-mon --cluster=ceph -i node1 -f
root      4687     1  0 17:19 ?        00:00:04 /usr/bin/ceph-osd --cluster=ceph -i 0 -f
root      5196     1  0 17:23 ?        00:00:00 ceph-mds --cluster mds -i 0 -m 192.168.1.176:6789
```
如果已经存在，下面步骤跳过，直接执行第10步的挂载

如果mds起不来，可以执行如下命令，查看具体报错信息
```
ceph@node1:~/opt/ceph$ sudo ceph-mds --cluster node1 -i 0 -m 192.168.1.176:6789 -f
starting mds.0 at :/0
2017-03-13 15:15:49.485908 7f32b07bb800 -1 did not load config file, using default settings.
2017-03-13 15:15:49.488414 7f32b07bb800 -1 monclient(hunting): ERROR: missing keyring, cannot use cephx for authentication
2017-03-13 15:15:49.490934 7f32b07bb800 -1 mds.-1.0 ERROR: failed to authenticate: (95) Operation not supported
```

> If starting the daemon fails with this error:
> mds.-1.0 ERROR: failed to authenticate: (22) Invalid argument
> Then make sure you do not have a keyring set in ceph.conf in the global section; move it to the client section; or add a keyring setting specific to this mds daemon. And verify that you see the same key in the mds data directory and ceph auth get mds.{id} output.

** 检查步骤 **  
查看如下结果是否一致
```
ceph@node1:~/opt/ceph$ ceph auth get mds.0
exported keyring for mds.0
[mds.0]
        key = AQBrR8ZY8GN+NxAAVu2pKRH0pXKixwca7HUdjA==
        caps mds = "allow"
        caps mon = "allow profile mds"
        caps osd = "allow rwx"


ceph@node1:~/opt/ceph$ cat /var/lib/ceph/mds/mds-0/keyring
[mds.0]
        key = AQBrR8ZY8GN+NxAAVu2pKRH0pXKixwca7HUdjA==
```

如果仍然报如上错误，可以尝试
```
ceph@node1:~/opt/ceph$ sudo chown -R ceph:ceph /var/lib/ceph/mds/mds-0
```
重新生成认证文件应该就会成功,<font color=red>不过不确定对后续操作的影响</font>

[也可以尝试这里的步骤，不过不保证会好使](https://www.sebastien-han.fr/blog/2013/05/13/deploy-a-ceph-mds-server/)


Now you are ready to create a Ceph filesystem.

## 10. 挂载

```
ceph@node1:~/opt/ceph$ sudo mkdir /mnt/cephfs
ceph@node1:~/opt/ceph$ sudo ceph-fuse -m {ip-address-of-monitor}:6789 /mnt/cephfs
ceph@node1:~/opt/ceph$ df -h
```

在node2上执行 安装ceph-fuse
```
ceph@node1:~/opt/ceph$ sudo apt-get install ceph-fuse
ceph@node1:~/opt/ceph$ sudo mkdir /mnt/cephfs
ceph@node1:~/opt/ceph$ sudo ceph-fuse -m {ip-address-of-monitor}:6789 /mnt/cephfs
ceph@node1:~/opt/ceph$ df -h
```

## 11. 注意事项
<font color=red>用户ceph不能够锁定，为了安全问题，只能将用户ceph改为nologin</font>
 
## 12. 服务器重启
服务器重启后，对于mon节点来说`osd`和`mon`会自动启动
osd节点的osd服务也会自动启动
 
 mon节点的`mds`服务需要手动启动，启动方法为
 ```
 sudo ceph-mds --cluster {cluster-name} -i {id} -m {mon-hostname}:{mon-port} [-f]
 ```
 **另**
 启动完mds后还需要执行挂载命令
 ```
 sudo ceph-fuse -m {ip-address-of-monitor}:6789 /mnt/cephfs
 ```
 可以将以上命令**放入到开机启动项中**，但是服务器重启完成后要进行检查ceph的运行状态
 `ceph -s`或者`ps -ef | grep ceph` 或者 通过`df -h`查看ceph-fuse是否成功挂载目录
 如果长时间没成功，并且`ceph -s`报错，可以尝试将另一节点node2也重启，node1可以成功挂载。

