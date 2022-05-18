# rn 引入firebase/crashlytics

https://rnfirebase.io/crashlytics/usage
用于app崩溃时生成报告，便于定位错误。firebase是google的。

1. 在谷歌注册自己的项目

https://console.firebase.google.com/u/0/

添加第1个app，包名必须和本地的一模一样

下载google-services.json
保存到android/app目录下

按照说明分别修改项目级和app级下的build.gradle

2. 确认/android/app/src/main/AndroidManifest.xml中的manifest标签中的包名和真实的包名一样。

3. cd android && ./gradlew signingReport生成多份variant key，将其中debugAndroidTest分类下的sha1和sha-256复制并添加到google的firebase console的自己的app中的SHA certificate fingerprints。

```
Valid until: 2051年12月19日 星期二
----------

> Task :react-native-safe-area-context:signingReport
Variant: debugAndroidTest
Config: debug
Store: C:\Android\.android\debug.keystore
Alias: AndroidDebugKey
MD5: 48:B7:35:9E:FC:F5:79:44:D2:F0:6A:D5:D5:A4:53:EA
SHA1: DD:4A:84:46:F3:98:56:86:05:DE:76:32:D9:1A:6C:60:ED:56:48:FA
SHA-256: 11:B7:CA:22:F6:28:42:CF:50:A4:95:C9:04:8C:0A:D5:95:31:5C:90:2F:EC:D4:AD:FB:A0:57:56:61:37:97:44 
```

4. yarn add ‘@react-native-firebase/app’

5. yarn add ’@react-native-firebase/crashlytics‘
然后额外配置：https://rnfirebase.io/crashlytics/android-setup
网址里面少了依赖，加上android/app/build.gradle: dependencies
implementation 'com.google.firebase:firebase-crashlytics'
