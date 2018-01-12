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
                compile 'com.github.wyba:jpush-library:V3.1.1'
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