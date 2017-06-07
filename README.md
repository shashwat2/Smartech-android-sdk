# Netcore Smartech Android SDK

[![Netcore Logo](https://netcore.in/wp-content/themes/netcore/img/Netcore-new-Logo.png)](http:www.netcore.in)

# Netcore Android SDK  

The Netcore Android SDK for App Engagement  

## Getting Started

Before performing Application Changes, Follow these two steps:-
 
Step 1: Get GCM sender Id and Api Key from Google Developer console
 
Step 2: Get App Id and Smartech SDK from Smartech Panel

Note: The SDK depends on Android v4 support library, minimum rev 23.1.1. The minimum Android SDK level supported is 9 (2.3)

### Changes in AndroidManifest File
1. Add GCM permissions in the androidmanifest.xml:
```xml
<uses-permission android:name="${applicationId}.permission.C2D_MESSAGE" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.GET_ACCOUNTS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.VIBRATE" />
<permission android:name="<package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
<uses-permission android:name="<package name>.permission.C2D_MESSAGE" />
```

2. To Add Below code in application tag for Application Id:
```xml
<meta-data
   android:name="com.netcore.sdk.ApplicationId"
   android:value="<AppId>"/>
```

3. To allow the receiver to receive notifications, Add Below code paste in application tag:
```xml
<meta-data
   android:name="com.netcore.sdk.ApplicationId"
   android:value="<AppId>"/>
<!-- GCM TO BE ADDED By CUSTOMER IN MANIFEST -->
<receiver
   android:name="com.google.android.gms.gcm.GcmReceiver"
   android:exported="true"
   android:permission="com.google.android.c2dm.permission.SEND">
   <intent-filter>
       <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
       <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
       
   </intent-filter>
</receiver>
<!-- GCM -->
```
4. Add Netcore SDK (i.e. aar file) in libs folder

5. Follow below instruction for modification of build.gradle:
 
Add in repositories below code:
 
    flatDir { dirs 'libs' } 
 
    Add in dependencies below code:
 
    compile(name:'<sdk name>', ext:'aar') 
 
    Add in dependencies for Google play services of GMS:
 
    compile "com.google.android.gms:play-services:7.5.+"


### SDK Method inside your app
1. Initialize of Smartech SDK Configuration in Launcher Activity
```java
//Add import files in Launcher page
 
import com.netcore.lib.ActivityLifeCycleCallBack;
import com.netcore.lib.NetcorePrefs;
import com.netcore.lib.NetcorePush;
import com.netcore.lib.NetcoreSDK;
 

// GCM
NetcoreSDK.Config config = new NetcoreSDK.Config()
.setSenderId("<sender Id>"); // Recieved from GCM

// Only for notification popup UI
config.setActivityClass(com.netcore.testapp1.MyActivity. class ).
setSmallIcon(R.mipmap.ic_launcher);

// Activity to be launched when user clicks on notification & Custom notification icon to be shown
// Only for data callbacks on payload
// Note :-Use this callback method to receive json data pushed from netCore server
config.setNetcorePush( new NetcorePush() {
    @Override
    public void onDataReceived(String json) {
        Log.d(TAG,"Recieved " + json);
    }
});
```
    
2. **Registration**
```java
//To register your app on netCORE’s server, add the following code in Launcher Activity:
// Current context
// Netcore.SDK.Config object instance.
// Note :- Call this on your landing screen to keep gcm tokens updated
try {
    NetcoreSDK. register ( this , config , currentTime in msecond , sessionId , identity ,
    NetcorePrefs. SIGNUP_REGISTER );
} catch (NetcoreSDK.MissingPermissionException e) {
    e.printStackTrace();
}
```

3. **Login**
```java
//Login your app to Netcore Server
//-Current context
//-userid / username/email that was used to login in your app
// Note :- To be used once you have registered on your app server and obtained primary id, you can then pass this ID as userId for identification purpose, this will help to forward notification to targeted user.

NetcoreSDK.login( this , UserName , CurrentTime in msecond , SesionId , NetcorePrefs.LOGIN,payload , config );

//Note: payload is optional. Put blank(i.e “”) if not passing anything
```

4. **Logout**
```java

NetcoreSDK.logout(this);
NetcoreSDK. logout ( this , identity , tx , sessionId , NetcorePrefs. LOGOUT , payload , config );

// Note: payload is optional. Put blank(i.e “”) if not passing anything
```

5. **Event Activity**
```java

//Event Activity
NetcoreSDK. registerAppEvent ( this , EventId, sessionId , CurrentTime in msecond , identity ,Payload , config );

// Note: payload is optional. Put blank(i.e “”) if not passing anything EventId pass by NetcorePrefs.<Event Name which provided by Us> 
// Ex. NetcorePrefs.ADD_TO_CART, NetcorePrefs.REMOVE_FROM_CART


```
6. **InApp Engage**
```java

//Add Below Code in OnCreate method of Launcher Activity
ActivityLifeCycleCallBack.register(getApplication());

```

### To support deeplink in application
Add these peice of code in AndroidManifest.xml file in each activity in which you want Deep-Link.
```xml
<intent-filter>
<action android:name = "android.intent.action.VIEW" />
<category android:name = "android.intent.category.DEFAULT" />
<category android:name = "android.intent.category.BROWSABLE" />
<data android:scheme = "<scheme name>" android :host= "<hostname>" />
</intent-filter>
Ex.
<intent-filter>
<action android:name= "android.intent.action.VIEW" />
<category android:name= "android.intent.category.DEFAULT" />
<category android:name= "android.intent.category.BROWSABLE" />
<data android:scheme = "smartech" android :host= "products" />
</intent-filter>
```

