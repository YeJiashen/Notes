Android8.0 SystemUi启动流程
1.	首先是系统服务SystemServer中的startOtherServices()方法中(此方法一千多行), mActivityManagerService.systemReady的回调中通过包名开启的SystemUiService;
	在开启服务后会调用WindowManagerService.onSystemUiStarted()方法通知SystemUi已经启动, 此方法最终调用进PhoneWindowManager, 使PhoneWindowManager与Keyguard进行绑定;
	在Android8.0中锁屏功能(Keyguard)归属于SystemUi之下, 所以需要等SystemUi启动完成后才可以进行服务绑定;
2.	当开始启动服务的时候, 首先启动的就是Application, 在这里也就是SystemUIApplication了;
3.	在Application中首先定义了两组服务SERVICES, SERVICES_PER_USER, 这两组服务均为SystemUI的子类实现;
	在Application的onCreate中, 首先设置全局主题, 然后创建一个SystemUIFactory的实例, 始终只保证SystemUIFactory只同时持有一个实例(类似单例但是这个单的对象不是永远的)
	其次是判断当前是否为系统进程, 如果是的话就注册一个广播监听, 对应的过滤器为Intent.ACTION_BOOT_COMPLETED, 优先级为系统最高, 它的主要作用是收到开机完成广播后依次通知SERVICES中的每个服务(功能)开机完成(onBootCompleted);
4.	然后服务启动, 会调用回SystemUIApplication中的startServicesIfNeeded, 这里面会start前面提到的服务(SERVICES或SERVICES_PER_USER中的服务);
5. 第一个启动的就是Dependency, 他是用来处理依赖关系的一个服务;
6. SystemBars -> StatusBar: 
	frameworks\base\packages\SystemUI\res\values\config.xml 
	<string name="config_statusBarComponent" translatable="false">com.android.systemui.statusbar.phone.StatusBar</string>
7. 