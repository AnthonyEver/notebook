# Data binding 入坑笔记二进阶篇之双向绑定



[ Data binding 入坑笔记一入门篇](http://blog.csdn.net/anthony_3/article/details/78575791)
>上一篇介绍了Data binding的基础用法，你可能会想这也太基础了，只支持前置数据的绑定，一旦数据变化了UI都监听不到。不要着急，这一篇就来讲到databinding的双向绑定用法。

## 0.举个例子
我们用一个输入控件EditText和一个文本控件TextView来举个栗子，我们想要在TextView要获取文本框内容，原始写法首先获取两个控件的对象，然后设置输入框监听对象，最后赋值给TextView做显示，算了算需要写三步呢
![这里写图片描述](http://img.blog.csdn.net/20171119185043722?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW50aG9ueV8z/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们来看看Data Binding的代码，只需要把对象绑定进去就好了
```
public class TwowayActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_twoway);
        ActivityTwowayBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_twoway);
        binding.setStudent(new Student());
    }
}
```
```
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    <data>
        <variable
            name="student" 
            type="com.jie.databindingsimple.entity.Student"/>
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">
        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="请输入内容"
            android:text="@={student.name}"/>
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{student.name}"/>
    </LinearLayout>
</layout>
```
##1. Observable Binding
Data Binding本身是不支持双向绑定的，我们想要实现双向绑定首先要改造一下实体类。让实体类继承`BaseObservable`，来看例子
```
public class Student extends BaseObservable {
    private String name;
    private String className;

    @Bindable
    public String getName() {
        return name;
    }

    @Bindable
    public String getClassName() {
        return className;
    }

    public void setName(String name) {
        this.name = name;
        notifyPropertyChanged(BR.name);
    }

    public void setClassName(String className) {
        this.className = className;
        notifyPropertyChanged(BR.className);
    }
}
```
`BR `是编译阶段生成的一个类，功能与 R.java 类似，用 `@Bindable`标记过 `getter `方法会在` BR `中生成一个静态常量。
```
public class BR {
        public static final int _all = 0;
        public static final int className = 1;
        public static final int name = 2;
        public static final int student = 3;
        public static final int user = 4;
}
```
在`setter`方法里我们需要用`notifyPropertyChanged`方法来更新对象。

还有另一种适用于POJO的写法，这种写法不需要继承`BaseObservable`，不过每个成员变量都要`new`一个`ObservableField`对象。
```
public class Teacher {
    public final ObservableField<String> name = new ObservableField<>();
    public final ObservableField<String> className = new ObservableField<>();
    public final ObservableInt age = new ObservableInt();
}
```
## 2. xml写法
如果想在输入的时候给变量赋值，则需要用到`@={}`这个操作符，`{}`里面是我们想要赋值的对象
```
<EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:hint="请输入内容"
    android:text="@={student.name}"/>
```
## 3.原理解析
`InverseBindingListener`是事件发生时触发的监听器，所有双向绑定，最后都是通过这个接口来`observable`改变的，各种监听，比如`TextWatcher`、`OnCheckedChange`，都是间接通过这个接口来通知的。在`ActivityTwowayBinding`的源码中我们可以找到这个方法片段。
```
    // values
    // listeners
    // Inverse Binding Event Handlers
    private android.databinding.InverseBindingListener mboundView1androidTextAttrChanged = new android.databinding.InverseBindingListener() {
        @Override
        public void onChange() {
            // Inverse of student.name
            //         is student.setName((java.lang.String) callbackArg_0)
            java.lang.String callbackArg_0 = android.databinding.adapters.TextViewBindingAdapter.getTextString(mboundView1);
            // localize variables for thread safety
            // student
            com.jie.databindingsimple.entity.Student student = mStudent;
            // student.name
            java.lang.String studentName = null;
            // student != null
            boolean studentJavaLangObjectNull = false;

            studentJavaLangObjectNull = (student) != (null);
            if (studentJavaLangObjectNull) {
                student.setName(((java.lang.String) (callbackArg_0)));
            }
        }
    };
```
我们通过` java.lang.String callbackArg_0 = android.databinding.adapters.TextViewBindingAdapter.getTextString(mboundView1);`获取到EditText上输入的值，来看看`getTextString`方法
```
    @InverseBindingAdapter(attribute = "android:text", event = "android:textAttrChanged")
    public static String getTextString(TextView view) {
        return view.getText().toString();
    }
```
`InverseBindingAdapter`用于关联某个用于接收`View`变更的方法，有两个参数`attribute `描述和`event`事件，获取到数据后我们拿到student对象，然后给他赋值`student.setName(((java.lang.String) (callbackArg_0)));`
## 4. 属性改变监听
如果有两个输入框，我们要监听输入对象的改变怎么办，如下：
![这里写图片描述](http://img.blog.csdn.net/20171119201432602?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYW50aG9ueV8z/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

我们可以利用`Observable `中的`addOnPropertyChangedCallback`来实现。先来看看`Observable `类，里面有添加回调、删除回调等方法。
```
package android.databinding;

public interface Observable {
    void addOnPropertyChangedCallback(Observable.OnPropertyChangedCallback var1);

    void removeOnPropertyChangedCallback(Observable.OnPropertyChangedCallback var1);

    public abstract static class OnPropertyChangedCallback {
        public OnPropertyChangedCallback() {
        }

        public abstract void onPropertyChanged(Observable var1, int var2);
    }
}
```
在`onCreate`中实现添加callback
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_twoway);
        ActivityTwowayBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_twoway);
        Student student = new Student();
        binding.setStudent(student);
        student.addOnPropertyChangedCallback(new Observable.OnPropertyChangedCallback() {
            @Override
            public void onPropertyChanged(Observable observable, int i) {
                switch (i) {
                    case BR.name:
                        Toast.makeText(TwowayActivity.this, "name", Toast.LENGTH_SHORT).show();
                        break;
                    case BR.className:
                        Toast.makeText(TwowayActivity.this, "classname", Toast.LENGTH_SHORT).show();
                        break;
                }
            }
        });
    }
```

## 5. 双向绑定场景范围
目前双向绑定仅支持如text，checked，year，month，hour，rating，progress等绑定。

[完整代码看这里](https://github.com/LaxusJie/DataBindingSimple/blob/master/app/src/main/java/com/jie/databindingsimple/TwowayActivity.java)



