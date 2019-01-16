由于需求, 项目由原本的x86架构迁移到了arm平台 ;
在进行应用移植的过程中, 编译通过但是安装过程中cmd提示INSTALL_FAILED_NO_MATCHING_ABIS错误 ;
这是由于项目使用了与当前平台不符的native libraries或是jni ;
解决方法为修改gradle配置文件使其针对不同架构打不同的包 ;
由于需要验证问题, 暂时用不到这个.so, 所以我选择了使用后面的方式 ;
具体配置为(Android Studio3.2, Gradle4.6, classpath3.1.2, buildToolVersion 27.0.3):
​	在主工程的build.gradle中进行如下配置:
​	1. 在最外层定义每个不同的abi对应的编码
​	2. 在android标签内加入splits标签并配置abi相关信息
​	3. 修改输出文件名, 防止在build apk时出现Several variant outputs are configured to use the same file name这个错误

具体如下:

```gradle
ext.abiCodes = ['arm64-v8a':2, armeabi:3, 'armeabi-v7a':4, mips:5, mips64:6, x86:7, 'x86_64':8]
android{
    ...
    splits {
        // Configures screen ABI split settings
        abi {
        // Enable ABI APK splits
        enable true

        // By default all ABIs are included, so use reset() and include to specify that we only
        // want APKs for x86, armeabi-v7a

        // Resets the list of ABIs that Gradle should create APKs for to none
        reset()

        // Specifies a list of ABIs that Gradle should create APKs for
        include 'x86', 'armeabi-v7a'

        // Specify that we want to also generate a universal APK that includes all ABIs
        universalApk true
        }
    }

    android.applicationVariants.all { variant ->
        variant.outputs.all { output ->
        def abiVersionCode = project.ext.abiCodes.get(output.getFilter("ABI"))
        outputFileName = "Captain_Settings_${defaultConfig.versionName}_${releaseTime()}_${variant.productFlavors[0].name}_${variant.buildType.name}_${abiVersionCode}.apk"
        }
    }
}
```

参考资料:

​	1. https://github.com/bugsnag/bugsnag-android-gradle-plugin/issues/103