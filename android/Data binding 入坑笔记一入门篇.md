# Data binding 入坑笔记一入门篇

数据绑定已经推出两年多的时间了，是时候下一波水了，边学习边记录一下实战步骤以及踩过得一些坑。

## 0. 什么是Data binding

Data Binding，顾名思义，数据绑定，是Google对MVVM在Android上的一种实现，可以直接绑定数据到xml中，并实现自动刷新。现在最新的版本还支持双向绑定，尽管使用场景不是那么多。

Data Binding可以提升开发效率（节省很多以往需要手写的java代码），性能高（甚至超越手写代码），功能强（强大的表达式支持）。

## 1. 准备环境

保证接入的项目gradle插件版本不低于 **1.5.0-alpha1**：

```
classpath 'com.android.tools.build:gradle:1.5.0'
```

在对应模块（Module）的build.gradle文件添加如下配置

```
dataBinding {
    enabled true
}
```

## 2.布局文件 

要使用Data Binding首先xml文件有了以下变化，他的根节点变成了layout，里面包含一个data节点和原根节点LinearLayout，其中这个data是用来绑定数据的。

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <!--这是原根节点-->
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"/>
   </LinearLayout>
</layout>
```

在data内可以利用variable声明变量，里面有两个属性name和type，name是变量的名字，type是变量的类型。(这里的User是一个实体类，下面说)

```
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
```

也可以把类型提出来用import导入

```
<data>
    <import type="com.example.User" />
    <variable name="user" type="User" />
</data>
```

基础类型可以直接使用，无需import

```
<variable name="userName" type="String" />
```

配置好布局文件后，IDE会根据xml文件的名称自动生成一个DataBinding类用于数据绑定，命名规则如下

```
activity_main.xml -> ActivityMainBinding
```

## 3. 数据对象

POJO类对象`User`，这种类型的对象具有从不改变的数据。在应用程序中，数据是一次读取，此后从不更改，这是很常见的。

```
public class User {
   public final String firstName;
   public final String lastName;
   public User(String firstName, String lastName) {
       this.firstName = firstName;
       this.lastName = lastName;
   }
}
```

也可以使用JavaBean形式

```
public class User {
   private final String firstName;
   private final String lastName;
   public User(String firstName, String lastName) {
       this.firstName = firstName;
       this.lastName = lastName;
   }
   public String getFirstName() {
       return this.firstName;
   }
   public String getLastName() {
       return this.lastName;
   }
}
```

## 4. 绑定数据

修改`Activity`中的`onCreate`方法，用`DataBindingUtil.setContentView`替代原来的`setContentView`方法从而实现数据绑定,创建`user`对象，通过`binding.setUser(user)`从而实现数据绑定

```
    private User user;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        user = new User("Laxus", "J");
        binding.setUser(user);
    }
```

值得一提的是`binding.setUser(user)`方法是由布局中的变量名自动生成的`get` 、 `set`方法

```
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
```

如果我们声明了一些基础变量，ActivityMainBinding也会相应的生成`get` 、 `set`方法

```
<data>
    <variable name="firstName" type="String" />
    <variable name="lastName" type="String" />
</data>
```

```
setFirstName(String firstName);
setLastName(String lastName);
```

## 5. 使用数据

现在我们就可以在xml的控件中使用数据绑定了

```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@{user.lastName}" />
```

查看完整代码