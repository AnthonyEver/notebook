# Android代码规范

## 注释

### 类注释

使用文档注释，举例：

```java
/**
 * desc：类名描述，要求简明直译.
 * author：haojie
 * date：2017-09-18
 */
```

### 方法注释

使用文档注释，举例：

```java
/**
 * 方法描述，要求简明直译.
 * @param xxx 参数名后必须填写说明
 * @return xxx 返回值后必须填写说明
 */
```

### 类成员变量和常量注释

使用单行注释，举例：

```java
//用户名
public static final String USER_ID = "userId";
```

### XML注释

布局和资源使用xml注释，举例：

```java
<!--顶部标题栏-->
<include
    layout="@layout/layout_createtheme_titlebar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

### gradle注释

使用单行注释，举例：

```java
//注解框架
compile 'com.jakewharton:butterknife:8.4.0'
//gson
compile 'com.google.code.gson:gson:2.2.4'
```

## 编码命名

### 包命名

包名一律小写，规则为[com].[公司名/组织名].[项目名].[模块名]，举例：

```
com.tfedu.yuwen.business
```

常见的包分层结构

* Fragment `com.xxx.xxx.fragments`
* 自定义view `com.xxx.xxx.view`
* 适配器`com.xxx.xxx.adapter`
* 工具`com.xxx.xxx.utils`
* 实体`com.xxx.xxx.entity`
* 服务`com.xxx.xxx.service`
* 广播`com.xxx.xxx.receiver`
* 数据库操作`com.xxx.xxx.db`

### 类和接口
特定的命名

* Activity `xxxActivity`
* Fragment `xxxFragment`
* Adapter`xxxAdapter`
* Service`xxxService`
* BroadcastReceiver`xxxReceiver`
* ContentProvider`xxxProvider`
* 数据库类`xxxDBHelper`
* 解析类`xxxParser`
* 自定义共享基类`BaseXxx`
* 工具类`xxxUtil``xxxManager`
* Handler`xxxHandler`
* 实体类`xxxEntity`
* 自定义view`xxxView`

### 方法
**以动词或动名词命名，采用驼峰命名法**

常见的命名场景

* 初始化相关方法，使用init为前缀标识，如初始化布局initView`initXxx()`
* 返回值为boolean类型的方法使用is前缀`isXx()`
* 返回某个值的方法，使用get为前缀`getXxx()`
* 对数据进行处理的方法，统一使用process为前缀`processXxx()`
* 弹出提示信息或者提示框，使用display作为前缀`displayXxx()`
* 保存数据相关的，使用save前缀`saveXxx()`
* 对数据重组的，使用reset前缀`resetXxx()`
* 清除数据的情况，使用clear前缀`clearXxx()`
* 删除控件的情况，使用remove前缀`removeXxx()`
* 绘制数据或效果相关的，使用draw为前缀`drawXxx()`
* 请求网络数据的，统一使用request为前缀`requestXxx()`
* 显示某布局的情况，使用show为统一前缀`showXxx()`

### 变量及常量

参考java编码规则中的定义

## 资源命名

### layout布局

1. Activity布局

项目中所有Activity页面对应的布局均采用**activity前缀 + 模块或者页面对应的英文名称**,布局中所有的均采用小写字母。举例：
```
//创建主题布局
activity_createtheme.xml
```
2. Fragment布局
  项目中所有Fragment页面对应的布局均采用**fragment前缀 + 模块或者页面对应的英文名称**,布局中所有的均采用小写字母。举例：
```
//讨论列表布局
fragment_discusslist.xml
```
3. 公共被引用layout
  布局中的共用布局片段抽取采用**layout前缀+模块+功能名称**。举例：
```
//评论模块音频片段
layout_comment_audio.xml
```
4.列表中的item
项目中列表页面中的item采用**item前缀+页面名称**。举例：
```
//讨论列表item
item_discuss.xml
```
5. 空数据页面
  项目中列表页面的空数据页面采用**empty前缀+页面名称**。举例：
```
//空数据页面
empty_message.xml
```
6. 自定义控件（组合布局）
  项目中自定义控件引用布局比如加载输入框布局采用**view前缀+布局名称**。举例：
```
//加载输入框样式
view_loading.xml
```
### drawable资源
1. selector类型
  项目中点击选中状态采用drawable前缀+使用**selector前缀+页面名称+事件**。举例：
```
//返回键选中状态事件
selector_discuss_back.xml
```
2. shape类型
  项目中图形绘制命名采用**shape前缀+使用场景名称+控件类型**。举例：
```
//学科按钮图形
shape_subject_btn.xml
```
### values资源
1. attrs属性资源
  项目中自定义view属性资源父级命名采用**大写开头驼峰形式的控件名称**命名，内部属性采用**小写开头驼峰形式的属性名称**命名。举例：
```java
<!--下拉控件属性-->
<declare-styleable name="DropDownMenu">
    <attr name="underlineColor" format="color"/>
    <attr name="dividerColor" format="color"/>
</declare-styleable>
```
2. colors颜色资源
  项目中颜色资源采用**color+颜色值小写**的形式命名，value值也均采用小写。举例：
```
    <color name="color_ffffff">#ffffff</color>
    <color name="color_f6f6f6">#f6f6f6</color>
```
3. string文字资源
  1. 公共文字资源采用**直接文字描述对应的英文名称**。举例：
```
<string name="theme">[主题讨论]</string>
<string name="writing">[写作]</string>
```
    2. 页面或组件特定的文字资源采用**模块名称+英文名称**。举例：
```
<string name="comment_hint">输入点评内容</string>
<string name="comment_success">发布成功！</string>
```
    3. 如果有不固定内容采用**占位符占位的形式**。举例：
```
<string name="discuss_speicial">%1$s专题</string>
```
4. styles样式资源
  项目中样式资源父级命名采用**大写开头驼峰形式的控件名称**命名，内部属性采用**小写开头驼峰形式的属性名称**命名。举例：
```java
<!-- 首页tablayout样式-->
    <style name="MainTabLayout" parent="Widget.Design.TabLayout">
    <item name="tabIndicatorColor">@color/color_f6f6f6</item>
    <item name="tabBackground">?attr/selectableItemBackground</item>
</style>
```
### mipmap图片资源
图片资源以特定的方式命名以后有很多好处，见名知意、方便查找、避免重复等等。图片建议全部使用png格式为标准。
1. 公用页面采用**common+图片的功能名称+事件状态**组合。举例：
```
common_back_pre.png
```
2. 页面中图片命名采用**页面英文名称+图片功能名称+事件状态**组合。举例：
```
discuss_publish_disable.png
```
常见的事件状态后缀
* 普通状态`nor`

* 按下状态`pre`

* 选中状态`sel`

* 未选中状态`unsel`

* 不可用状态`disable`

* 聚焦状态`focus`
### 布局中常用控件缩写
* TextView`tv`
* EditText`et`
* ImageView`iv`
* Button`btn`
* CheckBox`cb`
* RadioButton`rb`
* ProgressBar`pb`
* RatingBar`rat`
* LinearLayout`ll`
* RelativeLayout`rl`
* TableLayout`tl`
* FrameLayout`fl`
* ScrollView`sv`
* HorizontalScrollView`hsv`
* TabLayout`tl`
* CardView`cv`
* GridLayout`gl`
* RecyclerView`rv`

## java编码规约
### 命名规约
普通变量命名采用java命名规范，统一采用驼峰命名法
1. 代码中命名均不能以下划线或美元符号开始，也不能以下划线或者美元符号结束。
2. 代码中的命名严禁使用拼音和英文混合的方式，更不允许世界使用中文的方式。
3. 方法明、参数名、成员变量、局部变量都统一使用lowerCamelCase,必须遵从驼峰形式。
4. 常量命名全部大写，单词间用下划线隔开，力求语意表达完整清楚，不要嫌名字长。
5. 中括号是数组类型的一部分，数组定义如下：String[] args;
6. 抽象类命名使用Abstract或Base开头；异常类命名使用Exception结尾；测试类命名以它的名称开始，以Test结尾。
7. 杜绝完全不规范的缩写，避免望文不知义。
8. 如果使用到了设计模式，建议在类名中体现出具体模式。[例子：public class OrderFactory;  public class LoginProxy;  public class ResourceObserver;]
### 常量定义
常量定义在Android开发中非常常见，通常情况下**公用的常量都写到常量工具类中 utils包中的Constant类中。如果是某个类中特有的常量，都写在这个特定的类中,命名时不建议使用缩写**。公共常量的名字是注释的直译，以下划线隔开。特定类中的相同类型的常量最好有统一的前缀。
1. 公共类型常量。举例：
```
//发布类型   
public static final String PUBLISH_TYPE = “public_type”;
```
2. 特定类中的一系列常量。如果只有当前类使用请用private描述。举例：
```
//状态可用
private static final int STATUS_ENABLE = 0;
//状态不可用
private static final int STATUS_DISABLE = 1;
```
### 格式规约
1. 统一使用AndroidStudio格式化工具进行格式化
2. 统一使用AndroidStudio的Inspection工具进行检查校验
### OOP规约
1. 避免通过一个类的对象引用访问此类的静态变量或静态方法，无谓增加编译器解析成本，直接用类名来访问即可。
2. 所有的覆写方法，必须加@Override注解。
3. 相同的参数类型，相同的业务含义，才可以使用Java的可变参数，避免使用Object,可以变参数必须放在参数列表的最后。场景：一堆控件的显示和隐藏控制
4. Object的equals方法容易抛空指针异常，应使用常量或确定有值的对象来调用equals。
5. 构造方法里面禁止加入任何业务逻辑，如果有初始化的逻辑，请放在init方法中。
6. 循环体内，字符串的连接方式，使用StringBuilder的append方法进行扩展。
7. final可提高程序响应的效率，声明成final的情况：
  1. 不需要重新赋值的变量，包括类属性、局部变量
  2. 对象参数前加final，表示不允许修改引用的指向。
  3. 类方法确定不允许被重新。
8. 类成员与方法访问控制从严。【建议】
  1. 如果不允许外部直接通过new来创建对象，那么构造方法必须是private.
  2. 工具类不允许有public或default构造方法。
  3. 类非static成员变量并且与子类共享，必须是protected.
  4. 类非static成员变量并且仅在本类使用，必须是private。
  5. 类static成员变量如果仅在本类使用，必须是private.
  6. 若是static成员变量，必须考虑是否为final。
  7. 类成员方法只供类内部调用，必须是private。
  8. 类成员方法只对继承类公开，那么限制为protected.
## android编码规约【强制】

1. 代码中注释以外地方不要出现中文，可以通过strings.xml引用来显示中文
2. 控件的声明要放在activity和fragment级别，以便在整个界面使用
3. 每个界面的点击事件统一在一个方法中处理；不在xml中添加事件
4. strings.xml中使用%1sd等实现字符串的通配
5. 界面中的数据传递避免使用全局变量
6. 数据类型转换要进行校验
7. 使用常量代替枚举
8. 业务复杂的地方，可以抽取组件或者工具类；如果通用可以写到BaseActivity或者BaseFragment中
9. 类注释必须添加





## 分层

