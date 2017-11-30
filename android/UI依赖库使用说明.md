# UI依赖库使用说明

## 包含的依赖库

* buildsrc  aop的插件
* emoji  表情库
* functionbar  多功能栏库
* librarystar 星级库
* picture_library 图片选择库
* richeditor 富文本编辑器库
* SmallVideoLib 视频录制压缩库
* ucrop 图片裁切库

## 项目调用依赖库方法

所有依赖库均上传[私服](http://nexus.tfedu.net/#browse/browse/components:maven-releases)，在项目build.gradle中添加
```
allprojects {
    repositories {
        ...
        mavenCentral()
        maven { url "http://nexus.tfedu.net/repository/maven-releases/"}
    }
}
```
然后在app的build.gradle中添加

```
compile 'com.app:editor:1.0.1'
```

## 含有butterknife插件的依赖库

依赖库中包含butterknife插件的比较特殊，私服引用会有问题。先用本地aar导入方式替代，在app子目录下添加libs文件夹

![这里写图片描述](http://img.blog.csdn.net/20171128144051618?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW50aG9ueV8z/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

然后在app的build.gradle中添加

```
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
	...
    compile(name:'functionbar-release', ext:'aar')
    ...
}
```

## plugin引用方法

在项目build.gradle中添加
```
buildscript {
    repositories {
        ...
        maven { url "http://nexus.tfedu.net/repository/maven-releases/"}
    }
    dependencies {
        ...
        classpath 'com.app:aop-plugin:1.0.1'
    }
}
```

在app的build.gradle的上方添加
```
apply plugin: 'com.hc.gradle'
```

## 依赖库升级方案

以emoji库举例，修改代码并测试通过后在`gradle.properties`中修改pom参数升级版本号重新上传，上传策略可参考[发布library到私服](http://gitlab.tfedu.net/android-library/ui-lib/blob/master/%E5%8F%91%E5%B8%83Library%E5%88%B0%E7%A7%81%E6%9C%8D.md)