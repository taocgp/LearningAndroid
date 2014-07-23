#Service
>Android中的服务一般没有用户操作界面，用于开发如监控之类的程序。

```java
public class SMSService extends Service {
	@Override
	public IBinder onBind(Intent intent) {
		return null;
	}
}
```
在AndroidManifest.xml文件中的<application>节点里对服务进行配置:
`<service android:name=".SMSService" />`

服务不能自己运行，启动方法：
`Context.startService()`：不会多次创建服务（调用onCreate()方法），但会多次调用onStart()方法
只能调用`Context.stopService()`方法结束服务，服务结束时会调用onDestroy()方法。
```java
public class HelloActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		Button button =(Button) this.findViewById(R.id.button);
	
		button.setOnClickListener(new View.OnClickListener(){
			public void onClick(View v) {
				Intent intent = new Intent(HelloActivity.this, SMSService.class);
					startService(intent);
			}
		});
	}
}
```
`Context.bindService()`：不会导致多次创建服务及绑定(调用onCreate()、onBind()方法)。
调用者退出或解除绑定（调用unbindService()方法）：系统调用onUnbind()-->onDestroy()方法。
```java
class HelloActivity extends Activity {
	ServiceConnection conn = new ServiceConnection() {
			public void onServiceConnected(ComponentName name, IBinder service) {
			}
			public void onServiceDisconnected(ComponentName name) {
			}
	};
	@Override
	public void onCreate(Bundle savedInstanceState) {
		Button button =(Button) this.findViewById(R.id.button);
		button.setOnClickListener(new View.OnClickListener(){
			public void onClick(View v) {
				Intent intent = new Intent(HelloActivity.this, SMSService.class);
				bindService(intent, conn, Context.BIND_AUTO_CREATE);
				//unbindService(conn);//解除绑定
			}
		});
	}
}
```