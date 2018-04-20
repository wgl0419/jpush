# jpush

以更简便的方式接入极光推送SDK

## 目录

* [1.APK](#1apk)
* [2.Usage](#2usage)
    * [工程依赖 build.gradle](#工程依赖-buildgradle)
        * [(1).project](#1project)
        * [(2).app](#2app)
    * [Use it](#use-it)
        * [(1).配置JPUSH_APPKEY]()
        * [(2).配置AndroidManifest.xml]()
        * [(3).your application]()
        * [(4).设置别名]()
        * [(5).自定义通知栏]()
        * [(6).常用API]()
    
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







