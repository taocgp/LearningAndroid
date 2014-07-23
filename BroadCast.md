#广播机制
　　当系统/应用程序运行时便会向 Android 注册各种广播，Android 接收到广播会便会判断哪种广播需要哪种事件，然后向不同需要事件的应用程序注册事件，不同的广播可能处理不同的事件也可能处理相同的广播事件，这时就需要Android 系统为我们做筛选。
```java
public class BroadCastActivity extends Activity {
	public static final String ACTION_INTENT_TEST = "com.terry.broadcast.test";

	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		Button btn = (Button) findViewById(R.id.Button01);
		btn.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				Intent intent = new Intent(ACTION_INTENT_TEST);
				sendBroadcast(intent);
			}
		});
	}
}
　　
Public class SmsBroadCastReceiver extends BroadcastReceiver{
	@Override
	public void onReceive(Context context, Intent intent){
		Bundle bundle = intent.getExtras();
		Object[] object = (Object[])bundle.get("pdus");
		SmsMessage sms[]=new SmsMessage[object.length];
		for(int i=0;i<object.length;i++)
		{
			sms[0] = SmsMessage.createFromPdu((byte[])object<i>);
			Toast.makeText(context, "来自"+sms.getDisplayOriginatingAddress()+" 的消息是："+sms.getDisplayMessageBody(), Toast.LENGTH_SHORT).show();
		}
		//终止广播，可以根据用户输入的号码可以实现短信防火墙。
		abortBroadcast();
		
	}
}
```
　　一个广播接收器中处理多个动作：在Intent-filter 里面注册该动作，在onReceive 方法中，通过intent.getAction()判断传进来的动作即可做出不同的处理，不同的动作。
　　
注册广播接收器：筛选广播，每实现一个广播接收类必须在我们应用程序中的 manifest 中显式的注明哪一个类需要广播，并为其设置过滤器。
　　Action：要执行的动作，ACTION_VIEW,ACTION_EDIT

代码动态注册：不是常驻型广播，广播跟随程序的生命周期。
```java
		//生成广播处理
		smsBroadCastReceiver = new SmsBroadCastReceiver();
		//实例化过滤器并设置要过滤的广播
		IntentFilter intentFilter = new IntentFilter("android.provider.Telephony.SMS_RECEIVED");
		//注册广播
	BroadCastReceiverActivity.this.registerReceiver(smsBroadCastReceiver, intentFilter);//取消注册unregisterReceivera(receiver);
```

在AndroidManifest.xml中配置：常驻型，当应用程序关闭后，如果有信息广播来，程序也会被系统调用自动运行。
```xml
		<!--广播注册-->
		<receiver android:name=".SmsBroadCastReceiver">
			<intent-filter android:priority="20">
				<action android:name="android.provider.Telephony.SMS_RECEIVED"/>
			</intent-filter>
		</receiver>
```