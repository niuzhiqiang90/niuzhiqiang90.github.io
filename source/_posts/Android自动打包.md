---
title: Android自动打包
date: 2017-08-07 20:19:01
categories: android
tags: android
---

## 1. 配置Java环境
### 1.1 自行从网络下载jdk并配置环境变量
```
:~$ vi .bashrc
# set oracle jdk environment
export JAVA_HOME=/home/USERNAME/opt/jdk-1.8.0
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
```

### 1.2 生效配置
```
:~$ source ~/.bashrc
```

### 1.3 验证
```
:~$ java -version
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
```

## 2. 配置命令行工具
你可以使用命令行工具里的sdkmanager下载其他的SDK包。

### 2.1 下载
默认tools已经包含在Android Studio里了，如果你没有使用Android Studio，请访问[studio](https://developer.android.com/studio/index.html)下载相应的命令行工具包。
[linux版](https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip)

```
:~$ mkdir opt/android/
:~$ cd opt/android
:~/opt/android$ wget https://dl.google.com/android/repository/sdk-tools-linux-3859397.zip
```

### 2.2 解压
```
:~/opt/android$ unzip sdk-tools-linux-3859397.zip
:~/opt/android$ cd tools/bin/
:~/opt/android/tools/bin$ 
```

### 2.3 安装SDK
```
:~/opt/android/tools/bin$ mkdir /home/USERNAME/opt/android/sdk
:~/opt/android/tools/bin$ ./sdkmanager --sdk_root=/home/USERNAME/opt/android/sdk "build-tools;23.0.2"
:~/opt/android/tools/bin$ ./sdkmanager --sdk_root=/home/USERNAME/opt/android/sdk "platforms;android-23"
```
命令成功执行后  
会在`/home/USERNAME/opt/android/sdk`目录下，生成`build-tools`目录  
会在`/home/USERNAME/opt/android/sdk`目录下，生成`platforms`目录

```
:~/opt/android/tools/bin$ ls /home/USERNAME/opt/android/sdk/build-tools
23.0.2
:~/opt/android/tools/bin$ ls /home/USERNAME/opt/android/sdk/platforms
android-23
```

### 2.4 添加环境变量
```
:~/opt/android/tools/bin$ vi ~/.bashrc
```
添加如下内容
```
# set android SDK environment
export ANDROID_HOME=/home/USERNAME/opt/android
export PATH=$PATH:$ANDROID_HOME/tools
```

## 3. 安装gradle

### 3.1 下载gradle
[distributions链接](http://services.gradle.org/distributions/)  
[gradle-3.3-all链接](http://downloads.gradle.org/distributions/gradle-3.3-all.zip)

```
:~$ cd opt
:~/opt$ wget http://downloads.gradle.org/distributions/gradle-3.3-all.zip
```

### 3.2 解压gradle
```
~/opt$ unzip gradle-3.3-all.zip
```

### 3.3 配置环境变量
```
~/opt$ vi ~/.bashrc
```
添加如下内容
```
# set gradle environment
export GRADLE_HOME=/home/USERNAME/opt/gradle-3.3
export PATH=$GRADLE_HOME/bin:$PATH
```

### 3.4 生效配置
```
~/opt$ source ~/.bashrc
```

## 4. 打包应用
进入android项目所在目录，执行`gradle assembleRelease`即可自动打包应用。  
gradle在打包的过程中会自动下载所需要的依赖(默认会下载到`~/.gradle`目录)，速度可能比较慢，尤其是第一次执行，耐心等待即可。

另外，在执行过程中可能会报如下错误
```
FAILURE: Build failed with an exception.

* What went wrong:
A problem occurred configuring project ':app'.
> Could not resolve all dependencies for configuration ':app:_debugApk'.
   > A problem occurred configuring project ':weexplugin'.
      > Could not resolve all dependencies for configuration ':weexplugin:_debugCompileCopy'.
         > Could not find com.android.support:support-v4:23.1.1.
           Required by:
               project :weexplugin
               project :weexplugin > com.android.support:appcompat-v7:23.1.1

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.

BUILD FAILED
```
执行如下命令接受证书，即可解决
```
:~/opt/android/tools/bin$ ./sdkmanager --licenses
```

## 5. 加固app
使用各个android软件平台的加固工具或者脚本上传到对应的平台进行加固即可
```
:~/opt$ cd /home/USERNAME/platforms/xxx.mobile.web/platforms/android
:android$ bash jiagu.sh
:android$ cd /home/USERNAME/opt/android/apk
:~/opt/android/apk$ ls
app-release_101_jiagu_sign.apk
```
加固完成，即可进行发布上架。

