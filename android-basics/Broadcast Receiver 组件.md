## 广播机制简介

为什么说Android中的广播机制更加灵活呢？这是因为**Android中的每个应用程序都可以对自己感兴趣的广播进行注册**，这样该程序就只会收到自己所关心的广播内容，这些广播可能是来自于系统的，也可能是来自于其他应用程序的。

Android提供了一套完整的API，允许应用程序自 由地发送和接收广播。

- 用于发送广播机制的 Intent
- 接受广播的 BroadcastReceiver



### 广播类型

Android中的广播主要可以分为两种类型：标准广播和有序广播。

-  标准广播（normal broadcasts）是一种**完全异步执行**的广播，在广播发出之后，所有的 BroadcastReceiver几乎会在同一时刻收到这条广播消息，因此它们之间没有任何先后顺 序可言。这种广播的效率会比较高，但同时也意味着它是无法被截断的。标

- 有序广播（ordered broadcasts）则是一种**同步执行**的广播，在广播发出之后，同一时刻 只会有一个BroadcastReceiver能够收到这条广播消息，当这个BroadcastReceiver中的 逻辑执行完毕后，广播才会继续传递。所以此时的BroadcastReceiver是有先后顺序的， 优先级高的BroadcastReceiver就可以先收到广播消息，并且前面的BroadcastReceiver 还可以截断正在传递的广播，这样后面的BroadcastReceiver就无法收到广播消息了。





## 接受系统广播

Android内置了很多系统级别的广播，我们可以在应用程序中通过监听这些广播来得到各种系统的状态信息。比如手机开机完成后会发出一条广播，电池的电量发生变化会发出一条广播，系统时间发生改变也会发出一条广播，等等。



### 注册广播

如果想要接收这些广播，我们可以根据自己感兴趣的广播，自由地注册 BroadcastReceiver，这样当有相应的广播发出时，相应的 BroadcastReceiver 就能够收到该广播，并可以在内部进行逻辑处理。注册 BroadcastReceiver的方式一般有两种：

- 动态注册：在代码中注册。

  注意，动态注册的 BroadcastReceiver 一定要取消注册才行。

  ```
  // 创建 BroadcastReceiver 实例
  BroadcastReceiver receiver = new MyBroadcastReceiver();
  
  // 创建 IntentFilter
  IntentFilter filter = new IntentFilter();
  filter.addAction(Intent.ACTION_BOOT_COMPLETED);
  filter.addAction(ConnectivityManager.CONNECTIVITY_ACTION);
  
  // 注册 Receiver
  registerReceiver(receiver, filter);
  
  // 记得在适当时候取消注册（通常在 onDestroy 中）
  unregisterReceiver(receiver);
  ```

- 静态注册：在AndroidManifest.xml中注册。

  ```
  <receiver android:name=".MyBroadcastReceiver">
      <intent-filter>
          <action android:name="android.intent.action.BOOT_COMPLETED" />
          <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
      </intent-filter>
  </receiver>
  ```

  



### 自定义广播

只需新建一个类，让它继承自 BroadcastReceiver，并重写父类的onReceive()方法就行了。这样当有广播到来时， onReceive()方法就会得到执行，具体的逻辑就可以在这个方法中处理。

```java
public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        
        if (Intent.ACTION_BOOT_COMPLETED.equals(action)) {
            // 处理开机完成广播
            Toast.makeText(context, "系统启动完成", Toast.LENGTH_SHORT).show();
        } else if (ConnectivityManager.CONNECTIVITY_ACTION.equals(action)) {
            // 处理网络变化广播
            ConnectivityManager cm = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
            NetworkInfo info = cm.getActiveNetworkInfo();
            boolean isConnected = info != null && info.isConnected();
            Toast.makeText(context, "网络状态变化: " + (isConnected ? "已连接" : "已断开"), Toast.LENGTH_SHORT).show();
        }
    }
}
```





## 发送自定义广播

发送广播主要有两种类型：

- 发送标准广播
- 发送有序广播







## 最佳实践

### 实现强制下线

强制下线应该算是一个比较常见的功能，比如如果你的QQ号在别处登录了，就会将你强制挤下线。其实实现强制下线功能的思路比较简单，只需要在界面上弹出一个对话框，让用户无法进行任何其他操作，必须点击对话框中的“确定”按钮，然后回到登录界面即可。可是这样就会存 在一个问题：当用户被通知需要强制下线时，可能正处于任何一个界面，难道要在每个界面上 都编写一个弹出对话框的逻辑？如果你真的这么想，那思路就偏远了。我们完全可以借助本章所学的广播知识，非常轻松地实现这一功能。