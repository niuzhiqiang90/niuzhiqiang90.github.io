---
title: 使用vault来存储机密信息
date: 2019-02-14 13:26:24
tags: vault
---

## 0. 依赖问题
- vault是go语言编写，所以需要配置golang相关环境
- vault存储如果选用`consul`，需要配置部署`consul`
- 如果通过`supervisor`来管理，需要配置`supervisor`

## 1. 安装vault

### 1.1 下载安装
[vault下载地址](https://www.vaultproject.io/downloads.html)
下载并解压
例如
```
$ mkdir -p ~/opt/vault/{bin,conf}
$ unzip vault_1.0.1_linux_amd64.zip
$ mv vault ~/opt/vault/bin/
$ ln -s ~/opt/vault/bin/vault ~/bin/
```

### 1.2 修改配置
修改配置文件
```
$ vi ~/opt/vault/conf/config.json
```
内容如下
```
{
    "storage": {
        "consul": {
            "address": "127.0.0.1:8500",
            "path": "vault"
        }
    },
    "listener": {
        "tcp": {
            "address": "0.0.0.0:8200",
            "tls_disable": 1
        }
    },
    "disable_mlock": true
} 
```
这里指定使用`consul`来进行存储，所以要提前配置`consul`


### 1.3 启动
#### 1.3.1 直接通过命令启动
```
$ vault server -config=/home/USERNAME/opt/vault/conf/config.json
```
如果不使用`supervisor`来管理`vault`,下述步骤可忽略。

#### 1.3.2 通过supervisor管理
vault的supervisor配置文件如下
```
[program:vault]
user=USERNAME
environment=GOMAXPROCS=4
command=/home/USERNAME/opt/vault/bin/vault server -config=/home/USERNAME/opt/vault/conf/config.json
autostart=true
autorestart=true
stdout_logfile=/mnt/data/logs/supervisor/vault.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stderr_logfile=/mnt/data/logs/supervisor/vault.log
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
```
更新配置并启动
```
$ sudo supervisorctl update
$ sudo supervisorctl update vault
$ sudo supervisorctl status
```

## 2. vault初始化
### 2.1 导入环境变量
```
$ export VAULT_ADDR=https://localhost:8200
```

### 2.2 初始化
```
$ vault operator init
Unseal Key 1: i4K5DCbfe0iy6LcciewfEeCH2ljmkqR8lTtM+1051nTd
Unseal Key 2: 3ILnLasd11hbuKvRm40Ec4Eyb3dE5ZoON4yazePyxf3T
Unseal Key 3: 7C64bO5RbWfUWcBcAeVQKH5DGya887nr4T4iSW71PZsf
Unseal Key 4: CA8mQqAs/se6jMjbnhRsflehHK6URKde795j2ybufsGE
Unseal Key 5: lZeNsdvi/E4ykDG6d9FPFi7OFkuwAj6miHrFef5ebKxn

Initial Root Token: s.6HadHafLVhVWKy9LRaiLcpbK

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 3 key to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.
```
前5行是我们稍后用于解开Vault存储的主密钥。 
**vault仅在初始化期间显示解封密钥,以后不会再出现。**
记下并安全地存储它们，否则在服务器重启时我们将无法访问我们的机密信息！  
另外，请注意根令牌，因为我们稍后会需要它。与unseal键不同，root令牌可以在以后轻松生成，因此一旦完成所有配置任务就可以安全地销毁它。

### 2.3 导入token
```
$ export VAULT_TOKEN=s.6HadHafLVhVWKy9LRaiLcpbK
```

### 2.4 查看状态
```
$ vault status
Key                Value
---                -----
Seal Type          shamir
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    0/3
Unseal Nonce       n/a
Version            0.11.3
HA Enabled         false
```
到目前为止，vault还是密封的

## 3. vault解封
vault解封需要用到上述步骤中的key
```
$ vault operator unseal i4K5DCbfe0iy6LcciewfEeCH2ljmkqR8lTtM+1051nTd
$ vault operator unseal 3ILnLasd11hbuKvRm40Ec4Eyb3dE5ZoON4yazePyxf3T
$ vault operator unseal 7C64bO5RbWfUWcBcAeVQKH5DGya887nr4T4iSW71PZsf
```
发出每个命令后，vault将打印启封进度，包括它需要多少个共享。  
发送最后一个密钥共享后，我们会看到如下消息：
``` 
Key             Value
---             -----
Seal Type       shamir
Initialized     true
Sealed          false
Total Shares    5
Threshold       3
Version         1.0.1
Cluster Name    vault-cluster-d972c3af
Cluster ID      70faee28-ee84-f47d-3097-2b5e68eee71d
HA Enabled      true
HA Cluster      https://127.0.0.1:8201
HA Mode         active
```

## 4. 测试vault
使用键/值Secret
写入信息
```
vault kv put secret/aaa.bbb.data spring.datasource.username=mysql_dbowner spring.datasource.password=pwd4datasource
```
获取信息
```
$ vault kv get secret/aaa.bbb.data
=============== Data ===============
Key                           Value
---                           -----
spring.datasource.password    pwd4datasource
spring.datasource.username    mysql_dbowner
```

## 5. 策略
我们使用策略来创建更多受限制的令牌。
例如，我们可以定义一个策略，该策略只允许对我们之前使用的路径`secret/aaa.bbb.data`进行读访问

### 5.1 创建策略

```
$ vi ～/opt/vault/conf/aaa.bbb.data-policy.hcl
```
策略内容如下
```
path "secret/aaa.bbb.data" {
  capabilities = ["read"]
}
```

### 5.2 写入策略
```
vault policy write aaa.bbb.data aaa.bbb.data-policy.hcl
Success! Uploaded policy: aaa.bbb.data

$ vault policy list
default
aaa.bbb.data
root
```

### 5.3 测试策略
#### 5.3.1 生成token
```
$ vault token create -policy=aaa.bbb.data
Key                  Value
---                  -----
token                s.1qC6a2s0w7pat7x97xAOoqu1
token_accessor       6sYvJvTKhRY7YPLHQvlDlZkz
token_duration       768h
token_renewable      true
token_policies       ["default" "aaa.bbb.data"]
identity_policies    []
policies             ["default" "aaa.bbb.data"]
```
#### 5.3.2 测试该token只有读取权限，没有写入权限
```
$ export VAULT_TOKEN=s.1qC6a2s0w7pat7x97xAOoqu1

$ vault kv get secret/aaa.bbb.data
=============== Data ===============
Key                           Value
---                           -----
spring.datasource.password    pwd4datasource
spring.datasource.username    mysql_dbowner


$ vault kv put  secret/aaa.bbb.data aaa=bbb
Error writing data to secret/aaa.bbb.data: Error making API request.

URL: PUT http://localhost:8200/v1/secret/aaa.bbb.data
Code: 403. Errors:

* 1 error occurred:
        * permission denied
```
