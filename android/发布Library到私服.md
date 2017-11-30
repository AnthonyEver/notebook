## 发布Library到私服

先展示下我建立的Project的目录结果

![mark](http://7u2jwu.com1.z0.glb.clouddn.com/blog/20170421/155857574.png)

app目录大家最熟悉了，是AS默认的主module的名字，utils目录就是我新建立的library module。

接下来我们来配置maven的上传

### 配置pom参数

项目根目录下的gradle.property中添加如下pom参数

```
POM_NAME=utils
POM_VERSION=1.0.1
POM_ARTIFACTID=utils
POM_GROUPID=com.leplay.android
POM_PACKAGING=aar
POM_DESCRIPTION=utils for Android123456
```

### 配置Nexus参数

因为Nexus相关参数是固定的，包含仓库地址、用户名和密码，我们把这些参数写到gradle的Global配置中，目录是**C:\Users\(用户名)\.gradle\gradle.property**

```
NEXUS_USERNAME=username
NEXUS_PASSWORD=password
NEXUS_REPOSITORY_URL=http://xxx/nexus/content/repositories/xxx/123
```

### 引入upload脚本

在library的build.gradle文件末尾加上如下引用：

```
apply from: '../nexus_upload.gradle'
```

这个nexus_upload.gradle脚本包含生成java-source和java-doc，如果注释不完整可以注释掉脚本里的`androidJavadocsJar`调用，避免影响上传。

### uploadArchives

编译并上传library，双击右侧gradle task中的uploadArchives

![mark](http://7u2jwu.com1.z0.glb.clouddn.com/blog/20170421/161423364.png)

等待一会出现Success字样，证明已经上传成功 
![mark](http://7u2jwu.com1.z0.glb.clouddn.com/blog/20170421/161553992.png)

最后我们去Nexus上查看下，确实上传成功了

![mark](http://7u2jwu.com1.z0.glb.clouddn.com/blog/20170421/161717319.png)

## 使用Nexus私服上的Library

首先，要在项目的build.gradle里面声明私服的地址

![mark](http://7u2jwu.com1.z0.glb.clouddn.com/blog/20170421/161921072.png)

然后就是我们最熟悉的在module的build.gradle文件中添加依赖

```
compile 'com.leplay.android:utils:1.0.1'
```