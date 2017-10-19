# Android原生项目转RN项目

思路：官文是采用在项目基础上生成React结构，但问题颇多并不靠谱。生成React-Native项目然后替换android包更方便快捷。当前转换基于0.48版本。

## 替换项目

删除RN项目下android文件夹，将原生项目复制到RN项目路径下，并改名为android

## 配置settings.gradle

在最上方添加

```
rootProject.name = '项目名'
```

## 项目build.gradle中添加maven依赖

```
allprojects {
    repositories {
        jcenter()
        mavenLocal()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"
        }
    }
}
```

## 模块中gradle设置

### 将所有模块gradle中的version都统一为23
```
compileSdkVersion 23
buildToolsVersion "23.0.1"
```
### app.gradle增加ndk限制
```
defaultConfig {
    ...
    ndk {
    abiFilters "armeabi-v7a", "x86"
    }
}
```
### app.gradle增加依赖
```
dependencies {
    ...
    compile "com.facebook.react:react-native:+"  // From node_modules
    ...
}
```

## Application配置

需要注意的是0.48正式版新增了一个getJSMainModuleName方法来定位主模块入口

```
public class App extends Application implements ReactApplication{
    private static App mApp;

    public static App getInstance() {
        return mApp;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        mApp = this;
        //react-native
        SoLoader.init(this, /* native exopackage */ false);
    }

    //初始化react-native
    private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
        @Override
        public boolean getUseDeveloperSupport() {
            return BuildConfig.DEBUG;
        }

        @Override
        protected List<ReactPackage> getPackages() {
            return Arrays.<ReactPackage>asList(
                    new MainReactPackage(),
            );
        }

        @Override
        protected String getJSMainModuleName() {
            return "index";
        }
    };

    @Override
    public ReactNativeHost getReactNativeHost() {
        return mReactNativeHost;
    }

}
```
## 测试Activity设置

注意，一定要在根目录进行

```
public class MainActivity extends ReactActivity {

    @Override
    protected String getMainComponentName() {
        return "Yuwen";
    }
}
```

## Manifest.xml配置

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.tfedu.yuwen">
	
	<!--必要权限-->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_SETTINGS" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

	<!--不要忘记设置name-->
    <application
        android:name=".App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        
        <!-- React测试页面 -->
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!-- React设置页面 -->
        <activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
        
    </application>

</manifest>
```

## 运行

所有设置配置好后在根目录下执行指令react-native run-android就能运行了~

