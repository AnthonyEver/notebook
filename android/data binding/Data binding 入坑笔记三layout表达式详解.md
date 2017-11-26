---
layout: data
title: Data binding 入坑笔记三layout表达式详解
date: 2017-11-21
categories:
- Android Data Binding
tags:
- Data Binding
comments: true
---


![iceland](http://upload-images.jianshu.io/upload_images/2953846-0ac9a8cecb7914d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[Data binding 入坑笔记一入门篇](http://www.jianshu.com/p/3e4cd4d951e0)
[Data binding 入坑笔记二进阶篇之双向绑定](http://www.jianshu.com/p/2424306f9c76)

> 前两篇介绍了基础知识和双向绑定，今天我们来详细剖析一下layout语法规则，以便能灵活使用data binding

<!-- more -->

## 表达式概览
###  xml里支持使用以下表达式

* 数学 `+`` - ``/`` *`` %`
* 字符串连接 `+`
* 逻辑 `&&` `||`
* 二进制` &`` | ``^`
* 一元运算 `+`` -`` !`` ~`
* 移位 `>>`` >>>`` <<`
* 比较 `==`` >`` < ``>= ``<=`
* `instanceof`
* 分组` ()`
* `null`
* `Cast`
* 方法调用
* 数据访问 `[]`
* 三元运算 `?:`

**暂不支持以下表达式**

* this
* super
* new
* 显式泛型调用


## 表达式运用

### 变量
先来复习一下基础变量的使用，变量的使用分为变量给控件赋值和控件给变量赋值两种

#### 变量给控件赋值
```
android:text="@{student.name}"
```
#### 控件给变量赋值（具体请参考双向绑定）

```
android:text="@={student.name}"
```
#### 避免空指针

data binding会自动帮助我们进行空指针的避免，比如说@{student.name}，如果name是null的话，student.name则会被赋默认值（null）。int的话，则是0。

###  空运算符

```
android:text="@{user.firstName ?? user.lastName}"
```
这个操作等价于

```
android:text="@{user.displayName != null ? user.firstName : user.lastName}"
```
###  显隐控制
首先在 xml 的 `data` 节点中引用`View`
```
<data>
    <import type="android.view.View"/>
</data>
```
然后设置`visibility`
```
android:visibility="@{student.boy ? View.VISIBLE : View.INVISIBLE}"
```
### 静态方法调用
首先定义一个静态方法

```
public class StringUtil {
    /**
     * 实现文字倒序
     * @param text 文字
     * @return 倒序文字
     */
    public static String reverseString(String text) {
        StringBuffer stringBuffer = new StringBuffer(text);
        return  stringBuffer.reverse().toString();
    }
}
```
然后在 xml 的 `data` 节点中引用该类

```
<data>
    <import type="com.jie.databindingsimple.StringUtil" />
</data>
```
最后是方法的调用

```
android:text="@{StringUtil.reverseString(student.name)}"
```

### 监听方法调用
控件常用的监听都可以用事件绑定来搞定，比如以下这几种

* android:onClick
* android:onLongClick
* android:onTextChanged
  ...

#### 常规调用方法
首先在 xml 的 `data` 节点中添加变量，我这里是把当前activity对象添加进来
```
<data>
    <variable name="activity" type="com.jie.databindingsimple.LayoutActivity"/>
</data>
```
引用变量后别忘了在oncreate方法中绑定变量
```
ActivityLayoutBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_layout);

binding.setActivity(this);
```
最后在控件中绑定事件
```
<EditText...
    android:hint="事件监听，请输入文字"
    android:onTextChanged= "@{activity::onTextChanged}"/>
```
其他事件同理

#### Lambda表达式
在xml中我们还可以应用lambda表达式来书写

```
android:onClick="@{(view) -> activity.onTextClick(view)}"
```

#### 统计一下事件监听表达的用法
由此可见事件监听绑定有很多种用法，我们在项目中最好统一规范应用其中一种，避免个性化
```
android:onClick="onTextClick"
android:onClick="@{(view) -> activity.onTextClick(view)}"
android:onClick="@{activity::onTextClick}"
android:onClick="@{activity.onTextClick}"
```

###  资源数据
在xml中我们可以根据变量动态设置数值，比如这样

```
android:padding="@{large? @dimen/largePadding : @dimen/smallPadding}"
```
还可以拼接变量数据，比如这样

```
android:text="@{@string/nameFormat(firstName, lastName)}"
```

### 表达式链
重复的表达式

```
<ImageView android:visibility=“@{isVisible ? View.VISIBLE : View.GONE}”/>
<TextView android:visibility=“@{isVisible  ? View.VISIBLE : View.GONE}”/>
<CheckBox android:visibility="@{isVisible  ? View.VISIBLE : View.GONE}"/>
```
可以简化成下面这样

```
<ImageView aandroid:id="@+id/image"
 android:visibility="@{isVisible ? View.VISIBLE : View.GONE}"/>
<TextView android:visibility="@{image.visibility}"/>
<CheckBox android:visibility="@{image.visibility}"/>
```

**注意，id起名有一个bug，不能有下划线**`_`**否则会报错**

###  Include
include核心在`bind`属性，可以绑定`include`里`layout`的变量，先看主布局

```
<data>
    <variable name="btnText" type="String" />
</data>

<LinearLayout...>
    <include
        layout="@layout/layout_button"
        bind:btnText="@{btnText}"/>
</LinearLayout>
```
再看子布局，可见父布局用`bind`属性将`btnText`绑定了值，然后子布局进行赋值

```
<data>
    <variable name="btnText" type="String"/>
</data>

<LinearLayout...>
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="5dp"
        android:onClick="btnClick"
        android:text="@{btnText}"/>
</LinearLayout>
```
**注意的一点是，被include的布局必须顶层是一个ViewGroup，目前Data Binding的实现，如果该布局顶层是一个View，而不是ViewGroup的话，binding的下标会冲突（被覆盖），从而产生一些预料外的结果。**

### Viewstubs
在xml中添加ViewStub标签

```
<ViewStub
    android:id="@+id/view_stub"
    android:layout="@layout/view_stub"
    ... />
```

ViewStub比较特殊，在被实际`inflate`前是不可见的，所以使用了特殊的方案，用了`final`的`ViewStubProxy`来代表它，并监听了`ViewStub.OnInflateListener`

```
binding.viewStub.setOnInflateListener(new ViewStub.OnInflateListener() {
	@Override
	public void onInflate(ViewStub stub, View inflated) {
		ViewStubBinding binding = DataBindingUtil.bind(inflated);
		User user = new User("Laxus", "J");
		binding.setUser(user);
	}
});
```

## 总结
讲到这里layout所有表达式的用法基本全了，包含了各种各样的应用场景，基本可以告别了在代码中获取view对象来设置监听方法和赋值，从而避免了大量的冗余代码，回去可以狂删代码了。下一节我们来说说Data Binding在`Recycleview`中的用法。

[参考代码地址点这里](https://github.com/LaxusJie/DataBindingSimple)