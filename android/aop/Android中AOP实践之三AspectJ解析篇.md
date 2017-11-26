# Android中AOP实践之三AspectJ解析篇

# 介绍
AspectJ是Java的一个简单实用的面向方面的扩展。通过几个新的构造，AspectJ提供了对一系列横切关注的模块化实现的支持。

在现有的Java开发项目中采用AspectJ可能是一个简单而且增量的任务。一条路径是从开发方面开始，继续使用生产方面，然后在使用AspectJ建立经验之后再使用方面。采用也可以遵循其他途径。例如，一些开发人员将从马上使用生产方面受益。其他人可能几乎可以立即编写干净的可重用方面。

AspectJ支持基于名称和基于属性的横切。使用基于名称的横切的方面倾向于影响少数其他类。但是，尽管规模较小，但与普通的Java实现相比，它们通常可以消除显着的复杂性。使用基于属性的横切的方面可以具有小规模或大规模。

使用AspectJ会导致横切关注的干净模块化的实现。当作为AspectJ方面编写时，横切关注的结构是明确的且易于理解的。方面也是高度模块化的，使得开发横切功能的即插即用实现成为可能。
# 基础知识
## Join point 连接点
是指程序中可能作为代码注入目标的特定的点，例如一个方法调用或者方法入口。
程序中连接点有很多，下面做一个表格一一指出：
| Join Points           |   定义   |                     解释 |
| --------------------- | :----: | ---------------------: |
| method call           |  调用方法  |      一般在执行某个方法前会先调用该方法 |
| method execution      |  执行方法  |        这个点是已经执行到了方法的内部 |
| constructor call      | 调用构造方法 |                  同调用方法 |
| constructor execution | 执行构造方法 |                  同执行方法 |
| field get             |  获取参数  |       比如获取某个变量的值，get() |
| field set             |  设置参数  | 比如设置某个变量的值，int num = 3 |
| pre-initialization    |  预初始化  |          在第一次初始化前会预初始化 |
| initialization        |  初始化   |             初始化类的时候会执行 |
| static initialization | 静态初始化  |       静态块或静态类初始化的时候会执行 |
| handler               |  异常处理  |                        |
| advice execution      |  通知执行  |                        |

//这里可以用一个例子来演示一下所有的连接点

## Pointcut 切入点
一个程序中会有很多JPoint连接点，但不一定我们都要去关注。那么我们可以选择我们需要的点来作为切入点。

我们利用Pointcut的功能来筛选出对我们有用的点作为切入，pointcut有一套专门的语法，只要搞懂他后面就不愁了。
### 一个例子
```
    @Pointcut("within(@com.jie.aoptest.aop.DebugLog *)")
    public void withinAnnotatedClass() {}

    @Pointcut("execution(!synthetic * *(..)) && withinAnnotatedClass()")
    public void methodInsideAnnotatedType() {}

    @Pointcut("execution(@com.jie.aoptest.aop.DebugLog * *(..)) || methodInsideAnnotatedType()")
    public void method() {}
```
这个例子表明切入点在**DebugLog类中所有执行方法的点**，这里用到了within和execution两个指示符，within用于匹配指定类型内的方法执行，而exection则用于匹配方法执行的连接点。有synthetic标记的field和method是class内部使用的，正常的源代码里不会出现synthetic field。
### 切入点指示符
常用指示符
| 指示符         |                    说明                    |
| ----------- | :--------------------------------------: |
| execution   |               用于匹配方法执行的连接点               |
| within      |              用于匹配指定类型内的方法执行              |
| this：       | 用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配 |
| target      | 用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配 |
| args        |        用于匹配当前执行的方法传入的参数为指定类型的执行方法        |
| @within     |            用于匹配所以持有指定注解类型内的方法            |
| @target     |     用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解      |
| @args       |        用于匹配当前执行的方法传入的参数持有指定注解的执行         |
| @annotation |           用于匹配当前执行方法持有指定注解的方法            |

AspectJ切入点支持的切入点指示符还有： call、get、set、preinitialization、staticinitialization、initialization、handler、adviceexecution、withincode、cflow、cflowbelow、if、@this、@withincode，感兴趣的可以了解，就不一一说明了。
### 类型匹配语法
#### 先来看一下AspectJ类型匹配的通配符
1.   *：匹配任何数量字符；
2.   ..：匹配任何数量字符的重复，如在类型模式中匹配任何数量子包；而在方法参数模式中匹配任何数量参数。
3.   +：匹配指定类型的子类型；仅能作为后缀放在类型模式后边。
#### 接下来看一下具体匹配表达式类型
1. 注解：可选，方法上持有的注解，如@Deprecated；
2. 修饰符：可选，如public、protected；
3. 返回值类型：必填，可以是任何类型模式；“*”表示所有类型；
4. 类型声明：可选，可以是任何类型模式；
5. 方法名：必填，可以使用“*”进行模式匹配；
6. 参数列表：“()”表示方法没有任何参数；“(..)”表示匹配接受任意个参数的方法，“(..,java.lang.String)”表示匹配接受java.lang.String类型的参数结束，且其前边可以接受有任意个参数的方法；“(java.lang.String,..)” 表示匹配接受java.lang.String类型的参数开始，且其后边可以接受任意个参数的方法；“(*,java.lang.String)” 表示匹配接受java.lang.String类型的参数结束，且其前边接受有一个任意类型参数的方法；
7. 异常列表：可选，以“throws 异常全限定名列表”声明，异常全限定名列表如有多个以“，”分割，如throws java.lang.IllegalArgumentException, java.lang.ArrayIndexOutOfBoundsException。
### 组合切入点表达式
AspectJ使用 且（&&）、或（||）、非（！）来组合切入点表达式。
### 常用场景举例

## Advice通知参数
前面已经介绍了Join point 连接点和 Pointcut 切入点，如果基本掌握了的话那么恭喜你内功已经修炼7成了。

我们成功设置好切入点后需要获取通知来执行要切入的代码片段，这里的通知相当于钩子/回调方法，在程序执行到JPoint时候会调起通知，接下来就介绍一下获取通知的方式。

### Advice通知有三种类型
| 类型       |                    说明                    |
| -------- | :--------------------------------------: |
| before() |           是指在JPoint之前可以执行一些操作            |
| after()  |           是指在JPoint之后可以执行一些操作            |
| around() | 环绕JPoint执行操作，它包含了前后两个过程，使用这种类型需要手动调用procees方法来执行原操作 |

### 来看一个例子
我们来用检测是否登录做一个例子
这个是检查登录的注解
```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.CLASS)
public @interface CheckLogin {
}
```
先用before和after两个类型来做一个测试
```
    @Pointcut("execution(@com.jie.aoptest.aop.CheckLogin * *(..))")
    public void methodAnnotated() {
    }

    @Before("methodAnnotated()")
    public void beforeMethod(ProceedingJoinPoint joinPoint) {
        Log.d("aspect", "beforeMethod");
        Log.d("login", "请您登录");
        Toast.makeText(App.getAppContext().getCurActivity(), "请您登录", Toast.LENGTH_SHORT).show();
    }

    @After("methodAnnotated()")
    public void afterMethod(ProceedingJoinPoint joinPoint) {
        Log.d("aspect", "afterMethod");
    }
```
来看一下打印日志是这样的
```
11-12 06:30:52.476 10388-10388/com.jie.aoptest D/aspect: beforeMethod
11-12 06:30:52.477 10388-10388/com.jie.aoptest D/login: 请您登录
11-12 06:30:52.486 10388-10388/com.jie.aoptest D/aspect: afterMethod
```
然后我们用around做一个测试
```
    @Pointcut("execution(@com.jie.aoptest.aop.CheckLogin * *(..))")
    public void methodAnnotated() {
    }

    @Around("methodAnnotated()")
    public void aroundMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        Log.d("aspect", "aroundMethod");
        Log.d("login", "请您登录");
        Toast.makeText(App.getAppContext().getCurActivity(), "请您登录", Toast.LENGTH_SHORT).show();
        joinPoint.proceed();
        Log.d("aspect", "aroundMethod");
    }
```
打印日志是这样的
```
11-12 06:35:09.696 10512-10512/com.jie.aoptest D/aspect: aroundMethod
11-12 06:35:09.696 10512-10512/com.jie.aoptest D/login: 请您登录
11-12 06:35:09.700 10512-10512/com.jie.aoptest D/aspect: aroundMethod

```
两种方式都可以，但要注意一点**around和after两种类型是有冲突的，around和before可以共存**，所以还是建议两种方式，一种before和after配合使用，一种around单独使用。
### 参数的获取
#### 方法参数的获取
方法参数的获取很简单，可以通过joinPoint.getArgs()来获取参数，举个例子：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        safe("haha", 20, true);
        ...
    }
    
    @Safe
    private void safe(String a, int b, boolean c) {
        Log.d("aop", "获取参数")
    }
```
通知方法
```
    @Around("execution(!synthetic * *(..)) && methodAnnotated()")
    public void aroundJoinPoint(final ProceedingJoinPoint joinPoint) throws Throwable {
        for (Object arg : joinPoint.getArgs()) {
            Log.d("arg", arg.toString());
        }
        joinPoint.proceed(joinPoint.getArgs());
    }
```
日志打印
```
11-12 06:56:08.062 29915-29915/com.jie.aoptest D/arg: haha
11-12 06:56:08.062 29915-29915/com.jie.aoptest D/arg: 20
11-12 06:56:08.062 29915-29915/com.jie.aoptest D/arg: true
```
#### 注解参数的获取
直接上代码例子
首先需要在注解上声明参数
```
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface CheckPermission {
	//声明参数
    String declaredPermission();
}
```
然后看一下Activity中的调用方法，注意这里在注解后设置参数值
```
    @CheckPermission(declaredPermission="android.permission.READ_PHONE_STATE")
    private void checkPhoneState(){
        Log.d("CheckPermission","Read Phone State succeed");
    }
```
看一下切片类的写法，注意这里在切点上要用@annotation来获取注解对象，然后我们在aroundMethod方法中多了一个checkPermission对象，最后从这个对象中拿到注解参数
```
@Aspect
public class CheckPermissionAspect {

    @Pointcut("execution(@com.jie.aoptest.aop.CheckPermission * *(..)) && @annotation(checkPermission)")
    public void checkPermission(CheckPermission checkPermission){};

    @Around("checkPermission(checkPermission)")
    public void aroundMethod(JoinPoint joinPoint, CheckPermission checkPermission){
        //从注解信息中获取声明的权限。
        String neededPermission = checkPermission.declaredPermission();
        Log.d("CheckPermissionAspect", joinPoint.toShortString());
        Log.d("CheckPermissionAspect", "\tneeded permission is " + neededPermission);
    }
}
```
最后来看一下输出日志，证明我们已经成功拿到注解参数了
```
11-12 08:00:21.203 24559-24559/com.jie.aoptest D/CheckPermissionAspect: execution(MainActivity.checkPhoneState())
11-12 08:00:21.203 24559-24559/com.jie.aoptest D/CheckPermissionAspect: 	needed permission is android.permission.READ_PHONE_STATE
11-12 08:00:21.203 24559-24559/com.jie.aoptest D/CheckPermission: Read Phone State succeed
```
# 总结
AspectJ解析基本就到这里了，掌握了它就可以全面的用AOP思想去解决问题了，核心还是在解决问题的思路。我也是在边学习边整理从而写出这篇文档，这里讲解了一些AspectJ的基础用法，高级用法大家可以从参考文献的书中去慢慢探索。
# 参考文献
1. [深入理解Android之AOP](http://blog.csdn.net/innost/article/details/49387395) 博主写的细致入微，我也从中有所参考。
2. Manning.AspectJ.in.Action第二版 

