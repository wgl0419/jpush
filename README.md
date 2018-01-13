# jpush-library

Step 1. Add the JitPack repository to your build file

Add it in your root build.gradle at the end of repositories:

    	allprojects {
    		repositories {
    			...
    			maven { url 'https://jitpack.io' }
    		}
    	}

Step 2. Add the dependency

        dependencies {
                compile 'com.github.wyba:jpush-library:V3.1.2'
        }

Step 3. Use it

1.Add it in your app build.gradle

            android {
                compileSdkVersion 27
                buildToolsVersion "27.0.1"

                defaultConfig {
                    applicationId "com.ZKXT.demo"
                    minSdkVersion 14
                    targetSdkVersion 27
                    versionCode 1
                    versionName "1.0"
                    manifestPlaceholders = [
                            JPUSH_PKGNAME: applicationId,//applicationId
                            JPUSH_APPKEY : "d1f84c6d9ce2818bd549a550", //JPush上注册的包名对应的appkey.
                            JPUSH_CHANNEL: "developer-default", //暂时填写默认值即可.
                            UMENG_APPKEY:"593e444c717c195c86001b8b"//友盟
                    ]
                }

              }

2.配置AndroidManifest.xml

            <!-- 极光推送Required  一些系统要求的权限，如访问网络等-->
            <permission
                android:name="${JPUSH_PKGNAME}.permission.JPUSH_MESSAGE"
                android:protectionLevel="signature"/>
            <uses-permission android:name="${JPUSH_PKGNAME}.permission.JPUSH_MESSAGE" />
            <uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
            <uses-permission android:name="android.permission.INTERNET" />
            <uses-permission android:name="android.permission.WAKE_LOCK" />
            <uses-permission android:name="android.permission.READ_PHONE_STATE" />
            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
            <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
            <uses-permission android:name="android.permission.WRITE_SETTINGS" />
            <uses-permission android:name="android.permission.VIBRATE" />
            <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
            <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
            <!-- Optional for location -->
            <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" /> <!-- 用于开启 debug 版本的应用在6.0 系统上 层叠窗口权限 -->
            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
            <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
            <uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS" />
            <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
            <uses-permission android:name="android.permission.GET_TASKS" />

            在application节点下配置如下

               <!-- Rich push 核心功能 since 2.0.6-->
                    <activity
                        android:name="cn.jpush.android.ui.PopWinActivity"
                        android:exported="false"
                        android:theme="@style/MyDialogStyle"/>

                    <!-- Required SDK核心功能-->
                    <activity
                        android:name="cn.jpush.android.ui.PushActivity"
                        android:configChanges="orientation|keyboardHidden"
                        android:exported="false"
                        android:theme="@android:style/Theme.NoTitleBar">
                        <intent-filter>
                            <action android:name="cn.jpush.android.ui.PushActivity" />

                            <category android:name="android.intent.category.DEFAULT" />
                            <category android:name="${JPUSH_PKGNAME}" />
                        </intent-filter>
                    </activity>

                    <!-- Required SDK 核心功能-->
                    <!-- 可配置android:process参数将PushService放在其他进程中 -->
                    <service
                        android:name="cn.jpush.android.service.PushService"
                        android:exported="false"
                        android:process=":mult">
                        <intent-filter>
                            <action android:name="cn.jpush.android.intent.REGISTER" />
                            <action android:name="cn.jpush.android.intent.REPORT" />
                            <action android:name="cn.jpush.android.intent.PushService" />
                            <action android:name="cn.jpush.android.intent.PUSH_TIME" />
                        </intent-filter>
                    </service>
                    <!-- since 3.0.9 Required SDK 核心功能-->
                    <provider
                        android:name="cn.jpush.android.service.DataProvider"
                        android:authorities="${JPUSH_PKGNAME}.DataProvider"
                        android:exported="false" />

                    <!-- since 1.8.0 option 可选项。用于同一设备中不同应用的JPush服务相互拉起的功能。 -->
                    <!-- 若不启用该功能可删除该组件，将不拉起其他应用也不能被其他应用拉起 -->
                    <service
                        android:name="cn.jpush.android.service.DaemonService"
                        android:enabled="true"
                        android:exported="true">
                        <intent-filter>
                            <action android:name="cn.jpush.android.intent.DaemonService" />
                            <category android:name="${JPUSH_PKGNAME}" />
                        </intent-filter>

                    </service>
                    <!-- since 3.1.0 Required SDK 核心功能-->
                    <provider
                        android:name="cn.jpush.android.service.DownloadProvider"
                        android:authorities="${JPUSH_PKGNAME}.DownloadProvider"
                        android:exported="true" />
                    <!-- Required SDK核心功能-->
                    <receiver
                        android:name="cn.jpush.android.service.PushReceiver"
                        android:enabled="true"
                        android:exported="false">
                        <intent-filter android:priority="1000">
                            <action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED_PROXY" />   <!--Required  显示通知栏 -->
                            <category android:name="${JPUSH_PKGNAME}" />
                        </intent-filter>
                        <intent-filter>
                            <action android:name="android.intent.action.USER_PRESENT" />
                            <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                        </intent-filter>
                        <!-- Optional -->
                        <intent-filter>
                            <action android:name="android.intent.action.PACKAGE_ADDED" />
                            <action android:name="android.intent.action.PACKAGE_REMOVED" />

                            <data android:scheme="package" />
                        </intent-filter>
                    </receiver>

                    <!-- Required SDK核心功能-->
                    <receiver
                        android:name="cn.jpush.android.service.AlarmReceiver"
                        android:exported="false" />

                    <!-- User defined.  For test only  用户自定义的广播接收器-->
                    <receiver
                        android:name="com.zkxt.app.receiver.MyReceiver"
                        android:enabled="true"
                        android:exported="false">
                        <intent-filter>
                            <action android:name="cn.jpush.android.intent.REGISTRATION" /> <!--Required  用户注册SDK的intent-->
                            <action android:name="cn.jpush.android.intent.MESSAGE_RECEIVED" /> <!--Required  用户接收SDK消息的intent-->
                            <action android:name="cn.jpush.android.intent.NOTIFICATION_RECEIVED" /> <!--Required  用户接收SDK通知栏信息的intent-->
                            <action android:name="cn.jpush.android.intent.NOTIFICATION_OPENED" /> <!--Required  用户打开自定义通知栏的intent-->
                            <action android:name="cn.jpush.android.intent.CONNECTION" /><!-- 接收网络变化 连接/断开 since 1.6.3 -->
                            <category android:name="${JPUSH_PKGNAME}" />
                        </intent-filter>
                    </receiver>

                    <!-- User defined.  For test only  用户自定义接收消息器,3.0.7开始支持,目前新tag/alias接口设置结果会在该广播接收器对应的方法中回调-->
                    <receiver android:name="com.jpush.sdk.MyJPushMessageReceiver">
                        <intent-filter>
                            <action android:name="cn.jpush.android.intent.RECEIVE_MESSAGE" />
                            <category android:name="${JPUSH_PKGNAME}"/>
                        </intent-filter>
                    </receiver>
                    <!-- Required  . Enable it you can get statistics data with channel -->
                    <meta-data
                        android:name="JPUSH_CHANNEL"
                        android:value="${JPUSH_CHANNEL}" />
                    <meta-data
                        android:name="JPUSH_APPKEY"
                        android:value="${JPUSH_APPKEY}" /> <!--  </>值来自开发者平台取得的AppKey-->

3.your application

            @Override
            public void onCreate() {
                    JPushInterface.setDebugMode(true); 	// 设置开启日志,发布时请关闭日志
                    JPushInterface.init(this);     		// 初始化 JPush
            }

4.设置别名

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

5.自定义Receiver处理推送下来的消息

            package com.jpush.sdk;

            import android.content.BroadcastReceiver;
            import android.content.Context;
            import android.content.Intent;
            import android.os.Bundle;
            import android.text.TextUtils;

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
            //				processCustomMessage(context, bundle);

            			} else if (JPushInterface.ACTION_NOTIFICATION_RECEIVED.equals(intent.getAction())) {
            				Logger.d(TAG, "[MyReceiver] 接收到推送下来的通知");
            				int notifactionId = bundle.getInt(JPushInterface.EXTRA_NOTIFICATION_ID);
            				Logger.d(TAG, "[MyReceiver] 接收到推送下来的通知的ID: " + notifactionId);

            			} else if (JPushInterface.ACTION_NOTIFICATION_OPENED.equals(intent.getAction())) {
            				Logger.d(TAG, "[MyReceiver] 用户点击打开了通知");

            //				//打开自定义的Activity
            //				Intent i = new Intent(context, TestActivity.class);
            //				i.putExtras(bundle);
            //				//i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            //				i.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TOP );
            //				context.startActivity(i);

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

            //	//send msg to MainActivity
            //	private void processCustomMessage(Context context, Bundle bundle) {
            //		if (MainActivity.isForeground) {
            //			String message = bundle.getString(JPushInterface.EXTRA_MESSAGE);
            //			String extras = bundle.getString(JPushInterface.EXTRA_EXTRA);
            //			Intent msgIntent = new Intent(MainActivity.MESSAGE_RECEIVED_ACTION);
            //			msgIntent.putExtra(MainActivity.KEY_MESSAGE, message);
            //			if (!ExampleUtil.isEmpty(extras)) {
            //				try {
            //					JSONObject extraJson = new JSONObject(extras);
            //					if (extraJson.length() > 0) {
            //						msgIntent.putExtra(MainActivity.KEY_EXTRAS, extras);
            //					}
            //				} catch (JSONException e) {
            //
            //				}
            //
            //			}
            //			LocalBroadcastManager.getInstance(context).sendBroadcast(msgIntent);
            //		}
            //	}
            }

6.设置自定义通知栏

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

7.常用API

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







