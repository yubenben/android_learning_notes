#compileSdkVersion
compileSdkVersion是gradle用来编译的Android SDK版本。它不会改变运行时行为。所以强烈推荐使用最新的SDK进行编译。这个SDK可以帮助你检查代码，
找到新的编译警告和错误，避免使用废弃的API。

如果使用新的**Support Library**，那么引起最新的Support包时，要使用最新的comileSdkVersion

#minSdkVersion
minSdkVersion是应用可以运行的最低要求。你的应用的minSdkVersion必须大于你的库的最低版本。在少数情况下你仍然想用一个比你的应用的minSdkVersion
小的库,你可以使用 *tool:overrideLibrary* 标记，但请做彻底的测试

#targetSdkVersion
targetSdkVersion 是 Android 提供向前兼容的主要依据。在应用没有更新targetSdkVersion之前系统不会应用最新的行为变化。这允许你在适应新的行为变化
之前使用新的API。由于很多行为变化时非常明显的，所以将 target 更新为最新的 SDK 是所有应用都应该优先处理的事情。更新了targetSdkVersion 要做测试。

#Gradle和SDK版本
在你模块的build.gradle文件中设置：
```xml
android {
  compileSdkVersion 23
  buildToolsVersion "23.0.1"
 
  defaultConfig {
    applicationId "com.example.checkyourtargetsdk"
    minSdkVersion 7
    targetSdkVersion 23
    versionCode 1
    versionName "1.0"
  }
}
```

理想情况下，三者关系应该是
```xml
minSdkVersion (lowest possible) <= 
    targetSdkVersion == compileSdkVersion (latest SDK)
```


