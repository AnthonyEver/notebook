# 集成到现有原生Android应用错误集锦

## 错误1

变更sdk目录后adb找不到
解决方案：
	修改ANDROID_HOME

## 错误2
react-native run-android Android project not found
解决方案：	
	应该在根目录执行转换
	将项目名改为android
	setting中加rootProject.name = 'MyappProject'

## 错误3
curl指令找不到
解决方案：
	choco install curl
## 错误4
java.lang.UnsatisfiedLinkError: could find DSO to load: libreactnativejni.so
解决方案：
```
    defaultConfig {
        ...
        ndk {
            abiFilters "armeabi-v7a", "x86"
        }
        ...
    }
```
## 错误5

```
Caused by: java.lang.IllegalAccessError: Method 'void android.support.v4.net.ConnectivityManagerCompat.<init>()' is inaccessible to class 'com.facebook.react.modules.netinfo.NetInfoModule' (declaration of 'com.facebook.react.modules.netinfo.NetInfoModule' appears in /data/app/xxc.application1-1/base.apk)
```

解决方案：
	修改 app/build.gradle
	`compile 'com.android.support:appcompat-v7:23.0.1''`
	高版的会出问题，看生成的示例项目是用这个v7包，编译环境也是23
	方法来自：<https://github.com/facebook/react-native/issues/6152>

## 错误6

```
Native: Got JS Exception: TypeError: undefined is not a function (evaluating '(bridgeConfig.remoteModuleConfig||[]).forEach')
 11-05 12:20:24.257 4879-5218/xxc.application1 E/unknown:React: Exception in native call from JS
 com.facebook.react.bridge.JSExecutionException: TypeError: undefined is not a function (evaluating '(bridgeConfig.remoteModuleConfig||[]).forEach')
```
解决方案：
	因为项目根目录的build.gradle下没添加

```
 allprojects {    
     repositories {
         jcenter()        
             maven {            
                 // All of React Native (JS, Android binaries) is installed from npm
                 //特别注意！如果android项目工程与node_modules同级用
                 url "$rootDir/../node_modules/react-native/android"
                 如果node_modules内嵌至项目工程内
                 url "$rootDir/node_modules/react-native/android"
             }    
      }
 }
```

## 错误7
```
Caused by: java.lang.ClassCastException: android.app.Application cannot be cast to com.facebook.react.ReactApplication
```

解决方案：
	创建MainApplication并添加至注册文件

```
import android.app.Application;

import com.facebook.react.ReactApplication;
import com.facebook.react.ReactNativeHost;
import com.facebook.react.ReactPackage;
import com.facebook.react.shell.MainReactPackage;
import com.facebook.soloader.SoLoader;

import java.util.Arrays;
import java.util.List;

public class MainApplication extends Application implements ReactApplication {

  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    @Override
    public boolean getUseDeveloperSupport() {
      return BuildConfig.DEBUG;
    }

    @Override
    protected List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          new MainReactPackage()
      );
    }
  };

  @Override
  public ReactNativeHost getReactNativeHost() {
    return mReactNativeHost;
  }

  @Override
  public void onCreate() {
    super.onCreate();
    SoLoader.init(this, /* native exopackage */ false);
  }
}
```

## 错误8

![device-2017-09-29-110203](C:\Users\Administrator\Desktop\device-2017-09-29-110203.png)
application has not been registered
解决方案：
1. 保证MainActivity中getMainComponentName的项目名和index.android.js中的注册名一致

   ```
   public class MainActivity extends ReactActivity {

       /**
        * Returns the name of the main component registered from JavaScript.
        * This is used to schedule rendering of the component.
        */
       @Override
       protected String getMainComponentName() {
           return "MyappProject";
       }

   }
   ```

   ```
   AppRegistry.registerComponent('MyappProject', () => MyappProject);
   ```

2. 在运行RN代码的时候会出现这种错误，可能是因为你启动的服务器和当期运行的程序不是同一个   在启动服务和运行程序改为同一个就可以了

## 错误9

the development server returned response error code :500

解决方案：

	安装watchman ：http://facebook.github.io/watchman/docs/install.html
	执行语句：watchman watch-del-all
	清空android工程缓存

