#数据存储方式
##DataBase
>目录：/data/data/Package Name/database 。

##SharedPreferences
>目录：/data/data/Package Name/Shared_Pref

存储一些简单配置信息的一种机制，例如：登录用户的用户名与密码。其采用了Map数据结构来存储数据，以键值的方式存储，可以简单的读取与写入
```java

	void ReadSharedPreferences(){
		String strName,strPassword;
		SharedPreferences user = getSharedPreferences("user_info",0);
		strName = user.getString("NAME","");
		strPassword = user.getString("PASSWORD","");
	}
	
	void WriteSharedPreferences(String strName,String strPassword){
		SharedPreferences user = getSharedPreferences("user_info",0);
		user.edit();
		user.putString("NAME", strName);
		user.putString("PASSWORD",strPassword);
		user.commit();
	}
```
>限制：只能在同一个包内使用，不能在不同的包之间使用。

##文件
>自动目录：/data/data/Package Name/files

Context.openFileOutput(String fileName, int mode)和Context.openFileInput(String fileName)。参数fileName不可以包含路径分割符（如"/"）。
```java
		String fn = "moandroid.log";
		FileInputStream fis = openFileInput(fn);
		FileOutputStream fos = openFileOutput(fn,Context.MODE_PRIVATE);

```
>限制：只能在这个apk内访问。

##ContentProvider
>当应用继承ContentProvider类，并重写该类用于提供数据和存储数据的方法，就可以向其他应用共享其数据。好处：统一了数据访问方式。

###Uri：操作的数据
Uri主要包含了两部分信息：1.需要操作的ContentProvider ，2.对ContentProvider中的什么数据进行操作，一个Uri由以下几部分组成：

 - scheme：content://
 - 主机名（或Authority）：用于唯一标识
 - 路径（path）：可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下：
　　contact表中id为10的记录:/contact/10
　　contact表中id为10的记录的name字段：contact/10/name
　　contact表中的所有记录:/contact?
　　xml文件中contact节点下的name节点：/contact/name

字符串转换成Uri，可以使用Uri类中的parse()方法，如下：
```java
	String uriString = "content://com.changcheng.provider.contactprovider/contact";
	Uri uri = Uri.parse(uriString);
```

解析Uri，并从 Uri中获取数据
###UriMatcher：用于匹配Uri
 - 首先把你需要匹配Uri路径全部给注册上：
```java
        String contactPath = "com.changcheng.sqlite.provider.contactprovider";
		UriMatcher uriMatcher = new UriMatcher(UriMatcher.NO_MATCH);
		uriMatcher.addURI(contactPath, "contact", 1);
		uriMatcher.addURI(contactPath, "contact/#", 2);//#号为通配符
```
　　  第三个参数是匹配码
 - 注册完需要匹配的Uri后，就可以使用uriMatcher.match(uri)方法对输入的Uri进行匹配，	如果匹配就返回匹配码
###ContentUris：用于获取Uri路径后面的ID部分
 - withAppendedId(uri, id)：为路径加上ID部分
 - parseId(uri)：从路径中获取ID部分

###ContentResolver：外部应用对ContentProvider中的数据进行操作
 - getContentResolver()方法：获取ContentResolver对象，
 - insert、delete、update、query方法:操作数据。
