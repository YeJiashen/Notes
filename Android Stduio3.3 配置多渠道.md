Android Stduio3.3 配置多渠道
1. 可以配置多个风味渠道, 也就是flavorDimensions
	如果配置了两个风味渠道, 如flavorDimensions 'A', 'B', 而每个风味渠道都对应两个具体子渠道, 那么目前一共有A1, A2, B1, B2四个子渠道, 在选择生成Apk的时候, 就可以生成8个Apk, 分别为:
		|--> A1B1Debug
		|--> A1B1Release
		|--> A1B2Debug
		|--> A1B2Release
		|--> A2B1Debug
		|--> A2B1Release
		|--> A2B2Debug
		|--> A2B2Release
	渠道名的先后顺序由风味渠道定义变量的先后顺序决定
2. 可以通过渠道控制需要引入的lib/module, 具体用法为在导入的时候加上渠道前缀, 例如
	dependencies {
		...
		normalImplementation files('libs/normal.jar')
		niufaImplementation files('libs/niufa.jar')
		sdk27Implementation project(path: ':wireless-lib')
    	sdk28Implementation project(path: ':wireless-lib_28')
    	...
	}
	每一个风味渠道的子渠道都可以设置自己需要引入的lib/module, 不同渠道如果导入了同一个lib不会报错, 例如
	dependencies {
		...
	    sdk27Implementation files('libs/zixing-core-3.2.0.jar')
	    normalImplementation files('libs/zixing-core-3.2.0.jar')
	    ...
	}
3. 可以通过渠道控制targetSdkVersion, compileSdkVersion, minSdkVersion, applicationId等属性, 只需要在对应的渠道中进行复写即可, 例如
android {
    compileSdkVersion 27
    buildToolsVersion '27.0.3'
    defaultConfig {
        applicationId "com.linglongtech.settings"
        minSdkVersion 23
        targetSdkVersion 27
        ...
    }

    //渠道Flavors，
    flavorDimensions 'targetsdk','type'
    productFlavors {
        sdk27 {
            compileSdkVersion 27
            minSdkVersion 23
            targetSdkVersion 27
            dimension 'targetsdk'
        }

        sdk28 {
            compileSdkVersion 28
            minSdkVersion 23
            targetSdkVersion 28
            dimension 'targetsdk'
        }

        normal {
        	applicationId "com.linglongtech.normalsettings"
            dimension 'type'
        }
        niufa {
        	applicationId "com.linglongtech.niufasettings"
            buildConfigField 'boolean', 'needBind', 'false'
            buildConfigField 'String', 'btMode', '\"source\"'
            dimension 'type'
        }
    }

    ...
}