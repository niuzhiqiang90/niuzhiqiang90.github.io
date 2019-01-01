---
title: 更换linux系统的home磁盘
date: 2017-04-01 20:50:55
categories: linux
tags: linux
---

## 1. 源由
由于大量项目放到`/home`里且初期规划不合理导致磁盘空间严重不足，故需要择机对该磁盘进行更换，重新挂载到一块容量比较大的磁盘上，以满足日益增长的业务需求。


## 2. 添加磁盘并挂载
添加完磁盘，首先需要对磁盘进行分区格式化  

**部分常用`fdisk`命令如下**  

| command     | action     |
|:---------|:-----------|  
| l       | list known partition types |  
| n       | add a new partition     |
| p       | print the partition     |
| d       | delete a partition      |
| q       | quit without saving changes     |
| w       |  write table to disk and exit     |    

### 2.1 分区
<font color=red>分区时，要注意先通过`fdisk -l`命令，确定接下来要进行操作的设备文件名。  
之下的步骤，每一步都要格外仔细，一不小心就会造成数据丢失！</font>  
例如：我确认我新添的磁盘为`/dev/vdbc`，那进行分区的命令如下
```
sudo fdisk /dev/xvdc
```

### 2.2 格式化
通过`-t`指定磁盘要格式化成的类型，这里我要格式化成`ext4`
```
sudo mkfs -t ext4 /dev/xvdc1
```

### 2.3 创建临时挂载目录
```
sudo mkdir /mnt/tmp-home
```

### 2.4 挂载磁盘
```
sudo mount /dev/xvdc1 /mnt/tmp-home
```
至此，新添的磁盘已经进行了分区，格式化，挂载操作。  
接下来进行数据迁移，把之前的数据迁移到新磁盘上。

## 3. 同步数据
```
rsync -avzrt --delete-after /home/USERNAME /mnt/tmp-home/
```

## 4. 重命名之前的home目录
```
sudo mv /home/USERNAME /home/USERNAME-orig
```

## 5. 对比数据  
对比原来的`home`目录和新`home`目录的数据。  
例如：通过`du`命令来对比大小。
```
du -sh /home/USERNAME-orig
du -sh /mnt/tmp-home
```

## 6. 备份源数据
数据迁移完成后，可以将`USERNAME-orig`迁移到`/`下进行备份。

## 7. 自动挂载
```
sudo vi /etc/fstab
# 写入如下内容
/dev/xvdc1    /home    ext4      defaults    0  0
```

## 8. 重启服务器
```
sudo shutdown -r now
```

## 9. 验证
待服务器重启后，验证各个服务是否能够正常启动，尤其时`/home/USERNAME`下的项目能否正常运行。


## 10. 清除备份文件
验证各个服务和项目，在迁移到新的`/home`运行正常且稳定一段时间后，可以择机对原备份文件进行清理。



