# jpush

以更简便的方式接入极光推送SDK

## 目录

* [1.APK](#1apk)
* [2.Usage](#2usage)
    * [工程依赖 build.gradle](#工程依赖-buildgradle)
        * [(1).project](#1project)
        * [(2).app](#2app)
    * [Use it](#use-it)
        * [(1).配置JPUSH_APPKEY](#1配置jpush_appkey)
        * [(2).配置AndroidManifest.xml](#2配置androidmanifestxml)
        * [(3).your application](#3your-application)
        * [(4).设置别名](#4设置别名)
        * [(5).自定义通知栏](#5自定义通知栏)
        * [(6).常用API](#6常用api)
    
## 1.APK
 
 [Demo DownLoad](/app/release/app-release.apk?raw=true)
        
## 2.Usage

### 工程依赖 build.gradle

#### (1).project

    allprojects {
        repositories {
            ...
            maven { url 'https://jitpack.io' }
        }
    }

#### (2).app

    dependencies {
            compile 'com.github.wyba:jpush:V4.1.0'
    }

### Use it

#### (1).配置JPUSH_APPKEY

Add it in your app build.gradle

    android {
        ...
        defaultConfig {
            ...
            manifestPlaceholders = [
                   JPUSH_APPKEY : "d1f84c6d9ce2818bd549a550", //JPush上注册的包名对应的appkey.
            ]
        }
      }

#### (2).配置AndroidManifest.xml

    <!-- User defined.  For test only  用户自定义的广播接收器-->
    <receiver
        android:name="com.wyb.jpush.MyReceiver"
        android:exported="false"
        android:enabled="true">
        <intent-filter>
            <action android:name="cn.jpush.android.intent.REGISTRATION" /> <!--Required  用户注册SDK的intent-->
            <action android:name="cn.jpush.android.intent.MESSAGE_RECEIVED" /> <!--Required  用户接收SDK消息的intent-->
            <action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED" /> <!--Required  用户接收SDK通知栏信息的intent-->
            <action android:name="cn.jpush.android.intent.NOTIFICATION_OPENED" /> <!--Required  用户打开自定义通知栏的intent-->
            <action android:name="cn.jpush.android.intent.CONNECTION" /><!-- 接收网络变化 连接/断开 since 1.6.3 -->
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    
MyReceiver源码参考：

     package com.wyb.jpush;
     
     import android.content.BroadcastReceiver;
     import android.content.Context;
     import android.content.Intent;
     import android.os.Bundle;
     import android.text.TextUtils;
     
     import com.jpush.sdk.ExampleUtil;
     import com.jpush.sdk.LocalBroadcastManager;
     import com.jpush.sdk.Logger;
     
     import org.json.JSONException;
     import org.json.JSONObject;
     
     import java.util.Iterator;
     
     import cn.jpush.android.api.JPushInterface;
     
     /**
      * 自定义接收器
      * 
      * 如果不定义这个 Receiver，则：
      * 1) 默认用户会打开主界面
      * 2) 接收不到自定义消息
      */
     public class MyReceiver extends BroadcastReceiver {
     	private static final String TAG = "JIGUANG-Example";
     
     	@Override
     	public void onReceive(Context context, Intent intent) {
     		try {
     			Bundle bundle = intent.getExtras();
     			Logger.d(TAG, "[MyReceiver] onReceive - " + intent.getAction() + ", extras: " + printBundle(bundle));
     
     			if (JPushInterface.ACTION_REGISTRATION_ID.equals(intent.getAction())) {
     				String regId = bundle.getString(JPushInterface.EXTRA_REGISTRATION_ID);
     				Logger.d(TAG, "[MyReceiver] 接收Registration Id : " + regId);
     				//send the Registration Id to your server...
     
     			} else if (JPushInterface.ACTION_MESSAGE_RECEIVED.equals(intent.getAction())) {
     				Logger.d(TAG, "[MyReceiver] 接收到推送下来的自定义消息: " + bundle.getString(JPushInterface.EXTRA_MESSAGE));
     				processCustomMessage(context, bundle);
     
     			} else if (JPushInterface.ACTION_NOTIFICATION_RECEIVED.equals(intent.getAction())) {
     				Logger.d(TAG, "[MyReceiver] 接收到推送下来的通知");
     				int notifactionId = bundle.getInt(JPushInterface.EXTRA_NOTIFICATION_ID);
     				Logger.d(TAG, "[MyReceiver] 接收到推送下来的通知的ID: " + notifactionId);
     
     			} else if (JPushInterface.ACTION_NOTIFICATION_OPENED.equals(intent.getAction())) {
     				Logger.d(TAG, "[MyReceiver] 用户点击打开了通知");
     
     				//打开自定义的Activity
     				Intent i = new Intent(context, TestActivity.class);
     				i.putExtras(bundle);
     				//i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
     				i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP );
     				context.startActivity(i);
     
     			} else if (JPushInterface.ACTION_RICHPUSH_CALLBACK.equals(intent.getAction())) {
     				Logger.d(TAG, "[MyReceiver] 用户收到到RICH PUSH CALLBACK: " + bundle.getString(JPushInterface.EXTRA_EXTRA));
     				//在这里根据 JPushInterface.EXTRA_EXTRA 的内容处理代码，比如打开新的Activity， 打开一个网页等..
     
     			} else if(JPushInterface.ACTION_CONNECTION_CHANGE.equals(intent.getAction())) {
     				boolean connected = intent.getBooleanExtra(JPushInterface.EXTRA_CONNECTION_CHANGE, false);
     				Logger.w(TAG, "[MyReceiver]" + intent.getAction() +" connected state change to "+connected);
     			} else {
     				Logger.d(TAG, "[MyReceiver] Unhandled intent - " + intent.getAction());
     			}
     		} catch (Exception e){
     				e.printStackTrace();
     		}
     
     	}
     
     	// 打印所有的 intent extra 数据
     	private static String printBundle(Bundle bundle) {
     		StringBuilder sb = new StringBuilder();
     		for (String key : bundle.keySet()) {
     			if (key.equals(JPushInterface.EXTRA_NOTIFICATION_ID)) {
     				sb.append("\nkey:" + key + ", value:" + bundle.getInt(key));
     			}else if(key.equals(JPushInterface.EXTRA_CONNECTION_CHANGE)){
     				sb.append("\nkey:" + key + ", value:" + bundle.getBoolean(key));
     			} else if (key.equals(JPushInterface.EXTRA_EXTRA)) {
     				if (TextUtils.isEmpty(bundle.getString(JPushInterface.EXTRA_EXTRA))) {
     					Logger.i(TAG, "This message has no Extra data");
     					continue;
     				}
     
     				try {
     					JSONObject json = new JSONObject(bundle.getString(JPushInterface.EXTRA_EXTRA));
     					Iterator<String> it =  json.keys();
     
     					while (it.hasNext()) {
     						String myKey = it.next();
     						sb.append("\nkey:" + key + ", value: [" +
     								myKey + " - " +json.optString(myKey) + "]");
     					}
     				} catch (JSONException e) {
     					Logger.e(TAG, "Get message extra JSON error!");
     				}
     
     			} else {
     				sb.append("\nkey:" + key + ", value:" + bundle.getString(key));
     			}
     		}
     		return sb.toString();
     	}
     	
     	//send msg to MainActivity
     	private void processCustomMessage(Context context, Bundle bundle) {
     		if (MainActivity.isForeground) {
     			String message = bundle.getString(JPushInterface.EXTRA_MESSAGE);
     			String extras = bundle.getString(JPushInterface.EXTRA_EXTRA);
     			Intent msgIntent = new Intent(MainActivity.MESSAGE_RECEIVED_ACTION);
     			msgIntent.putExtra(MainActivity.KEY_MESSAGE, message);
     			if (!ExampleUtil.isEmpty(extras)) {
     				try {
     					JSONObject extraJson = new JSONObject(extras);
     					if (extraJson.length() > 0) {
     						msgIntent.putExtra(MainActivity.KEY_EXTRAS, extras);
     					}
     				} catch (JSONException e) {
     					e.printStackTrace();
     				}
     			}
     			LocalBroadcastManager.getInstance(context).sendBroadcast(msgIntent);
     		}
     	}
     }

#### (3).your application

            @Override
            public void onCreate() {
                    JPushInterface.setDebugMode(true); 	// 设置开启日志,发布时请关闭日志
                    JPushInterface.init(this);     		// 初始化 JPush
            }

#### (4).设置别名

            private void setAlias(Context context, String alias) {
                // 调用 JPush 接口来设置别名。
                JPushInterface.setAliasAndTags(context,
                        alias, null, new TagAliasCallback() {
                            @Override
                            public void gotResult(int code, String s, Set<String> set) {
                                switch (code) {
                                    case 0:
                                        // 建议这里往 SharePreference 里写一个成功设置的状态。成功设置一次后，以后不必再次设置了。
        //                  MyApplication.getSp().edit().putBoolean("setAlias",true).apply();
                                        break;
                                    case 6002:
                                        break;
                                    default:
                                }
                            }
                        });
            }


#### (5).自定义通知栏

        // 设置自定义通知栏
        public static void setJpushNotification() {
            BasicPushNotificationBuilder builder = new BasicPushNotificationBuilder(
                    appContext);
            builder.statusBarDrawable = R.mipmap.logo;
            // 设置为自动消失
            builder.notificationFlags = Notification.FLAG_AUTO_CANCEL;

            // 设置为只有震动和铃声
            builder.notificationDefaults = Notification.DEFAULT_VIBRATE
                    | Notification.DEFAULT_SOUND;

            JPushInterface.setDefaultPushNotificationBuilder(builder);
            JPushInterface.setPushNotificationBuilder(1, builder);
        }

#### (6).常用API

            API - stopPush

            停止推送服务。

            调用了本 API 后，JPush 推送服务完全被停止。具体表现为：

                收不到推送消息
                极光推送所有的其他 API 调用都无效,不能通过 JPushInterface.init 恢复，需要调用resumePush恢复。

            API - resumePush

            恢复推送服务。

            调用了此 API 后，极光推送完全恢复正常工作。

            API - isPushStopped

            用来检查 Push Service 是否已经被停止

                SDK 1.5.2 以上版本支持。

            API addLocalNotification 添加一个本地通知

            API removeLocalNotification 移除指定的本地通知

            API clearLocalNotifications 移除所有的本地通知







