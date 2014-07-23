#引入第三方jar包 (Android Studio)

 - 打开Module buid.gradle文件
 - 将jar包拷到指定的位置
 - 加到list当中去，从dependencies中移到list里

 ```json
List myDependencies = ["com.android.support:appcompat-v7:18.0.+", 
fileTree (dir: 'dir\path\to\file', includes: ['filename.jar'])]
dependencies {
    compile myDependencies
} 
 ```