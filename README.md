# Netcore Smartech Android SDK

[![Netcore Logo](https://netcore.in/wp-content/themes/netcore/img/Netcore-new-Logo.png)](http:www.netcore.in)

# Netcore Android SDK  

The Netcore Android SDK for App Engagement  

## Getting Started

Before performing Application Changes, Follow these two steps:-
 
Step 1: Get GCM sender Id and Api Key from Google Developer console
 
Step 2: Get App Id and Smartech SDK from Smartech Panel

Note: The SDK depends on Android v4 support library, minimum rev 23.1.1. The minimum Android SDK level supported is 14 (4.0)

### Changes in AndroidManifest File
1. Add Below code above or below application tag in the androidmanifest.xml:
```xml
        <uses-permission android:name="${applicationId}.permission.C2D_MESSAGE" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
        <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.SEND_SMS"></uses-permission>
        <uses-permission android:name="android.permission.RECEIVE_SMS"></uses-permission>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <permission android:name="<package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        ```

2. Add Below code in application tag for Application Id:
```xml
<meta-data
   android:name="com.netcore.sdk.ApplicationId"
   android:value="<AppId>"/>
```
3. To allow the receiver to receive notifications, Add Below code in application tag:
```xml
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

### Follow below instruction for modification of build.gradle:
    Add in parent repositories (not in child repositories. If not present then create new repositories) below code:
 
    flatDir { dirs 'libs' } 
 
    Add in parent dependencies (not in child dependencies. If not present then create new dependencies) below code:
 
    compile(name:'SmartechSDK', ext:'aar') 
 
    Add in parent dependencies (not in child dependencies. If not present then create new dependencies) for Google play services of GMS:
 
    compile "com.google.android.gms:play-services:7.5.+"


### SDK Method inside your Application
1. Initialize of Smartech SDK Configuration in Launcher Activity
```java
//Add import files in Launcher page
 
import com.netcore.lib.ActivityLifeCycleCallBack;
import com.netcore.lib.NetcorePrefs;
import com.netcore.lib.NetcorePush;
import com.netcore.lib.NetcoreSDK;
 

// Define Variables as
  NetcoreSDK.Config config;
  public long sessionId;
 
 //Paste below code in onCreate method:
  sessionId = System.currentTimeMillis();
  config = new NetcoreSDK.Config()
  .setSenderId("<sender Id>");

// Only for notification popup UI 
config.setActivityClass( MainActivity.class ).
       setSmallIcon( R.drawable.ic_launcher ); //change as per your available icon in drawable

// Activity to be launched when user clicks on notification & Custom notification icon to be shown
// Only for data callbacks on payload
// Note :-Use this callback method to receive json data pushed from netCore server
config.setNetcorePush( new NetcorePush() {
    @Override
    public void onDataReceived(String json) {
    NetcoreSDK.registerAppEvent( MainActivity.this, NetcorePrefs.PUSH_DELIVERED, System.currentTimeMillis(), sessionId, identity, "", config );
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
    NetcoreSDK. register ( this , config , System.currentTimeMillis(), sessionId , identity ,
    NetcorePrefs.SIGNUP_REGISTER );
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

NetcoreSDK.login( this , UserName , System.currentTimeMillis(), sesionId , NetcorePrefs.LOGIN, payload , config );

//Note: payload is optional. Put blank(i.e “”) if not passing anything
```

4. **Logout**
```java

NetcoreSDK.logout( this, identity, System.currentTimeMillis(), sessionId, NetcorePrefs.LOGOUT, payload, config );

// Note: payload is optional. Put blank(i.e “”) if not passing anything
```

### Event Activity Tracking For Every Activity
```java

//Add this line for every Activity which is going to Track
NetcoreSDK.registerAppEvent( this, EventId, sessionId, System.currentTimeMillis(), identity, Payload, config );

// Note: payload is optional. Put blank(i.e “”) if not passing anything EventId pass by NetcorePrefs.<Event Name which provided by Us> 
// Ex. NetcorePrefs.ADD_TO_CART, NetcorePrefs.REMOVE_FROM_CART


```
### Changes for InApp Engage
```java

//Add Below Code in OnCreate method of Launcher Activity
ActivityLifeCycleCallBack.register(getApplication());

```
### For Profile Update
```java

//Add below code in activity where you passing all details for profile:-
 
 NetcoreSDK.profile(this,newProfile);
 
Note: newProfile is JSONObject with all details of User
E.g. 
JSONObject newProfile = new JSONObject();
try {
  newProfile.put( "NAME", "Developer" );
  newProfile.put( "AGE", 25 );
  newProfile.put( "MOBILE", "1234567890" );
  newProfile.put( "EMAIL", "abc@example.cmo" );
  newProfile.put( "COUNTRY", "India" );
  newProfile.put( "EDUCATION", "B.E." );
  newProfile.put( "CITY", "Mumbai" );
  newProfile.put( "GENDER", "Male" );
  newProfile.put( "DOB", "12/12/2012" );
  newProfile.put( "MARRIED", "single" );
 
  NetcoreSDK.profile(this,newProfile);
}
catch ( JSONException e ) {
  e.printStackTrace();
}

```

### Go to tools->android and click on sync project with gradle files

### Run the application

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

