#NDK
##从java调用C
- java声明本地方法
>声明native方法（不实现）

- 实现JNI粘合层

>```shell
cd ~/workspace/MyApp
mkdir jni
javah -classpath bin -jni -d jni com.justao.myapp.myclass
```
javah -classpath bin -stubs com\_justao\_myapp\_Myclass -d jni 生成com_justao_myapp_Myclass.c文件
```c
jtype JNICALL
Java_com_justao_myapp_Myclass_myfunc
  (JINEnv *env,jclass clazz,jint n){
  return 0;
}
```  
方法非静态时，第二个参数为jobject类型

- 创建makefile

>在jni文件夹创建Application.mk
```make
APP_ABI := all
```  
在jni文件夹创建Android.mk
```make
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE    := module_name
LOCAL_SRC_FILES := com_justao_myapp_Myclass.c
include $(BUILD_SHARED_LIBRARY)
```

- c实现

>创建另外的.c文件和.h文件，在com_justao_myapp_Myclass.c里调用

- 编译本地库

>在JNI目录使用nkd命令构建共享库

- 加载本地库

>在声明本地方法的类里面的静态初始化块里
```java
    static {
    System.loadLibrary("module_name");
    }
``` 


##Native Activity
```xml
<activity android:name="android.app.NativeActivity"
          android:lable="@string/app_name">
    <meta-data android:name="android.app.lib_name"
               nadroid:value="myapp" />
    <meta-data android:name="android.app.func_name"
               nadroid:value="ANativeActivity_onCreate" />
</activity>
```