#Activity生命周期详解
##Activity的生命周期
 - **onCreate**:创建界面、数据的初始化
 - **onStart**:变成用户可见不可交互的
 - **onResume**:变成和用户可交互的，（在activity 栈系统通过栈的方式管理这些个Activity的最上面，运行完弹出栈，则回到上一个Activity)
 - **onPause**:到这一步是可见但不可交互 的，系统会停止动画 等消耗CPU 的事情从上文的描述已经知道，应该在这里保存你的一些数据,因为这个时候你的程序的优先级降低，有可能被系统收回。在这里保存的数据，应该在onResume里读出来，注意：这个方法里做的事情时间要短，因为下一个activity不会等到这个方法完成才启动
 - **onstop**:变得不可见，被下一个activity覆盖了
 - **onDestroy**: 这是activity被干掉前最后一个被调用方法了，可能是外面类调用finish方法或者是系统为了节省空间将它暂时性的干掉，可以用isFinishing()来判断它，如果你有一个Progress

Dialog在线程中转动，请在onDestroy里把他cancel掉，不然等线程结束的时候，调用Dialog的`cancel`方法会抛异常的。
`onPause`，`onstop`， `onDestroy`，三种状态 下 activity都有可能被系统干掉
为了保证程序的正确性，你要在`onPause()`里写上持久层操作的代码，将用户编辑的内容都保存到存储介质上（一般都是数据库）。实际工作中因为生命周期的变化而带来的问题也很多，比如你的应用程序起了新的线程在跑，这时候中断了，你还要去维护那个线程，是暂停还是杀掉还是数据回滚，是吧？因为Activity可能被杀掉，所以线程中使用的变量和一些界面元素就千万要注意了，一般我都是采用Android的消息机制 [Handler,Message]来处理多线程和界面交互的问题。这个我后面会讲一些，最近因为这些东西头已经很大了，等我理清思绪再跟大家分享。

##在被系统回收之前保存当前状态
当你的程序中某一个Activity A 在运行时中，主动或被动地运行另一个新的Activity B,这个时候A会执行
```java
public void onSaveInstanceState(Bundle outState) {
		super.onSaveInstanceState(outState);
		outState.putLong("id", 1234567890);
}
```
B完成以后又会来找A, 这个时候就有两种情况，一种是A被回收，一种是没有被回收，被回收的A就要重新调用onCreate()方法，不同于直接启动的是这回onCreate()里是带上参数savedInstanceState，没被收回的就还是onResume就好了。
　　savedInstanceState是一个Bundle对象，你基本上可以把他理解为系统帮你维护的一个Map对象。在onCreate()里你可能会用到它，如果正常启动onCreate就不会有它，所以用的时候要判断一下是否为空。
```java
		if(savedInstanceState != null){
			long id = savedInstanceState.getLong("id");
		}
```
例子：读取列表:滚动条的位置、编辑note:id


##将Activity设置成窗口的样式
设置 一下Activity的主题：在AndroidManifest.xml 中定义Activity的地方一句话：

对话框
```xml
            android :theme="@android:style/Theme.Dialog"
```
半透明
```xml
            android:theme="@android:style/Theme.Translucent"
```
类似的这种activity的属性可以在`android.R.styleable` 类的`AndroidManifestActivity` 方法中看到，`AndroidManifest.xml`中所有元素的属性的介绍都可以参考这个类`android.R.styleable`值是在android.R.style中可以看到，比如这个`@android:style/Theme.Dialog`就对应于`android.R.style.Theme_Dialog`

##安全退出已调用多个Activity的Application　　
对于单一Activity的应用来说，退出很简单，直接`finish()`即可。当然，也可以用`killProcess()`和`System.exit()`这样的方法。现提供几个方法，供参考：

 - 抛异常强制退出：该方法通过抛异常，使程序Force Close。验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出Force Close的窗口。
 - 记录打开的Activity：每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。
 - 发送特定广播：在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。
 - 递归退出在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。除了第一个，都是想办法把每一个Activity都结束掉，间接达到目的。但是这样做同样不完美。你会发现，如果自己的应用程序对每一个Activity都设置了nosensor，在两个Activity结束的间隙，sensor可能有效了。但至少，我们的目的达到了，而且没有影响用户使用。为了编程方便，最好定义一个Activity基类，处理这些共通问题。