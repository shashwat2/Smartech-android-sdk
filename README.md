# Netcore Smartech Android SDK

[![Netcore Logo](https://netcore.in/wp-content/themes/netcore/img/Netcore-new-Logo.png)](http:www.netcore.in)

# Netcore Android SDK  

The Netcore Android SDK for App Engagement  

## Getting Started

1. Sign Up

    Signup to Netcore Smartech account.

2.  Install the SDK

## Android Studio / Gradle     

### Changes in /libs folder        
    Copy the '.aar' file in the /libs directory of your project and also add it as a dependency.

Note: The SDK depends on Android v4 support library, minimum rev 23.1.1. The minimum Android SDK level supported is 9 (2.3)

### Changes in Manifest File
1. Add GCM permissions in the androidmanifest.xml . The Application id is the package id of your application.
```xml
<uses-permission android :name= "${applicationId}.permission.C2D_MESSAGE" />
<uses-permission android :name= "android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android :name= "android.permission.INTERNET" />
<uses-permission android :name= "android.permission.WAKE_LOCK" />
<uses-permission android :name= " com.google.android.c2dm.permission.RECEIVE" />
<permission
    android:name="="${applicationId}.permission.C2D_MESSAGE"
    android :protectionLevel= "signature" />
```

2. To allow the receiver to receive notifications, Add the following between the
<application></application> tags.

```xml
<receiver
android:name="com.google.android.gms.gcm.GcmReceiver"
android:exported="true"
android:permission="com.google.android.c2dm.permission.SEND">
<intent-filter>
< action android:name="com.google.android.c2dm.intent.RECEIVE"/>
</intent-filter>
<!--If you want to support pre-4.4 KitKat devices, add the following action to
the intent filter declaration for the receiver
<action android :name= "com.google.android.c2dm.intent.REGISTRATION" />
</receiver>
```

3. To allow deep link notification, include these intent filters inside <activity></activity> tags.

```xml
<intent-filter android :label= "sample">
<action android:name="android.intent.action.VIEW">
<category android:name="android.intent.category.DEFAULT"/>
<category android:name="android.intent.category.BROWSABLE"/>
<!-- Accepts URIs that begin with "http://www.example.com/sample”-->
<data android:host= "www.example.com" android:pathPrefix= "/sample" android:scheme= "http"/>
<!-- note that the leading "/" is required for pathPrefix-->
<!-- Accepts URIs that begin with "example://sample” →
</ intent-filter >
```



### SDK Method inside your app
1. Initialize Smartech SDK Configuration
```java

Obtain the sender ID from Google, and the application ID from Smartech.
// GCM
NetcoreSDK.Config config = new NetcoreSDK.Config()
.setSenderId("<xxxxxxxxxx>") // Recieved from GCM
.setApplicationId("<xxxxxxxxxxxxxxx>"); // Present in Smartech UI
// Or you can Include Application id in following meta-tag inside application tag
<meta-data
android:name="com.netcore.sdk.ApplicationId"
android:value="@string/app_id"/> // app_id - Present in Smartech UI

// Only for notification popup UI
config .setActivityClass(com.netcore.testapp1.MyActivity. class ).
setSmallIcon(R.mipmap.ic_launcher);

// Activity to be launched when user clicks on notification & Custom notification icon to be shown
// Only for data callbacks on payload
// Note :-Use this callback method to receive json data pushed from netCore server
config .setNetcorePush( new NetcorePush() {
    @Override
    public void onDataReceived(String json) {
        Log.d(TAG,"Recieved " + json);
    }
});
```

2. **Registration**
To register your app on netCORE’s server, add the following <>
```java
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

