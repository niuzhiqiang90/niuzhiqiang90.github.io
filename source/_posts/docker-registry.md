---
title: docker-registry
date: 2017-02-27 19:44:00
tags: docker
---

## 1. 启动registry
```
docker run -d -p 5000:5000 --name registry registry
```
**说明**

| 参数                      | 说明                                                    |  
| -----                     | ------                                                  |
| `-p 5000:5000`              | 第一个是host的端口，第二个是container的端口             |
| `--name registry registry1` | registry是镜像名字，registry1是自定义的私有registry名字 |

## 2. 测试私有仓库

### 2.1 新pull一个镜像
```
sudo docker pull busybox
```

### 2.2 给镜像打个tag
```
sudo docker tag busybox xxx.xxx.xxx.xxx:5000/myfirstimage
```
### 2.3 push到私有仓库
```
sudo docker push xxx.xxx.xxx.xxx:5000/myfirstimage
```

### 2.4 查看私有镜像仓库中的镜像
```
curl http://xxx.xxx.xxx.xxx:5000/v2/_catalog
```

### 2.5 删除本地的镜像
```
sudo docker rmi xxx.xxx.xxx.xxx:5000/myfirstimage
```

### 2.6 将刚才的删除的镜像，从私有镜像仓库pull回来
```
sudo docker pull xxx.xxx.xxx.xxx:5000/myfirstimage
```


## 3. 报错及处理
### 3.1 在本地push时，出现如下错误
```
sudo docker push xxx.xxx.xxx.xxx:5000/myfirstimage
The push refers to a repository [xxx.xxx.xxx.xxx:5000/myfirstimage]
Get https://xxx.xxx.xxx.xxx:5000/v1/_ping: http: server gave HTTP response to HTTPS client
```
**解决方案**
1. 修改配置
```
sudo vi /etc/default/docker
```
2. 添加如下内容
```
DOCKER_OPTS="--insecure-registry xxx.xxx.xxx.xxx:5000"
```
3. 重启docker服务
```
sudo service docker restart
```
4. 重新执行
```
sudo docker push xxx.xxx.xxx.xxx:5000/myfirstimage
```

