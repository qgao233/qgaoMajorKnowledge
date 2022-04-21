# 安卓相关

## 修改版本号
**app/build.gradle**
```
defaultConfig {
    applicationId "com.qgao2021"
    minSdkVersion rootProject.ext.minSdkVersion
    targetSdkVersion rootProject.ext.targetSdkVersion
    versionCode 2       //自己看的
    versionName "0.1.2"   //用户看的
}
```

## 修改app名称
**android\app\src\main\res\values\strings.xml**
```
<resources>
    <string name="app_name">点子</string>
</resources>
```

## proguard混淆

ProGuard工作原理：https://blog.csdn.net/ljd2038/article/details/51308768
proguard 混淆规则：https://www.cnblogs.com/skymxc/p/proguard.html
