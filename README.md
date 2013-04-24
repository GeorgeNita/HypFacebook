HypFacebook
=============================
A Facebook native extension for NME
-----------------------------

This NME native extension allows you to integrate Facebook into your NME application.

It integrates the [Facebook iOS SDK 3.2.1](https://github.com/facebook/facebook-ios-sdk)
and the [Facebook Android SDK 3.0.1](https://github.com/facebook/facebook-android-sdk)

These are under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html)

Installation
------------

There is an [include.nmml](https://github.com/hyperfiction/HypFacebook/blob/master/include.nmml) file and [ndll](https://github.com/hyperfiction/HypFacebook/tree/master/ndll) are compiled for:
* ios armv6
* ios armv7
* ios simulator
* android armv6


iOS
---
For iOS you need to [install the FacebookSDK](https://developers.facebook.com/ios/) to be able to link the FacebookSDK.framework in XCode.

Take a look at [the facebook docs](http://developers.facebook.com/docs/getting-started/facebook-sdk-for-ios/) for more info, in particular the " Configure a new Xcode Project" section.

Add a FacebookDisplayName key in your app-Info.plist with your app name:

```xml
  <key>FacebookDisplayName</key>
  <string>_________</string>

```
Add a CFBundleURLTypes key in your app-Info.plist with "fb<appid>":

```xml
  <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>fb_______________</string>
            </array>
        </dict>
    </array>

```
Also mind to add the FacebookSDK.framework and the bundles,
to the framework folder in XCode before building. Check that you
choose "Create groups for any added folders"
and deselect 'Copy items into destination group's folder (if needed)'.

If you target ios < 6, toggle Security, Social, Accounts and AdSupport
to optional.

On iOS when you present a dialog it will always trigger "DIALOG_CANCELED" even if the dialog is successful, because of [this facebook bug](https://developers.facebook.com/bugs/234604753348378).

Android
-------
Add the LoginActivity to your AndroidManifest.xml

Copy the res content from the extension in the templates/android/res
folder and merge files if you have several native extensions.

Copy the MainActivity.java to the java src folder with your package name
Example with the template tag in the nmml file :

```xml
<template path="Export/android/bin/MainActivityFacebook.java"
   rename="src/my/package/name/MainActivity.java"/>

```

Recompiling
-----------

For recompiling the native extensions just use the sh files contained in the project folder

Usage
-----

```haxe
    var fb = new HypFacebook( "<your appid>" );
    fb.addEventListener( HypFacebookEvent.OPENED, _onFbOpened );
    fb.connect( true ); // false to disallow login UI

    function _onFbOpened( _ ) {
		fb.call( GRAPH_REQUEST("/me") );
    }

```
The allowUI parameter of the connect function allow to present the login page if there is no cached/active token. You should call connect( false ) first to check if there is an active token. If not, then present a login button to the user that call connect( true ).

Once HypFacebook is opened, you pass a FacebookRequest enum value to the call function:

```haxe
    DIALOG        	( sAction   	: String, ?params : Hash<String> );
    FEED_DIALOG   	( params    	: Hash<String> );
    REQUEST_DIALOG	( params    	: Hash<String> );
    GRAPH_REQUEST 	( sGraphPath	: String, ?params : Hash<String>, ?sMethod : HTTP_METHOD );

```
And listen to the result event:

```haxe
    class HypFacebookEvent extends Event{

		public var sFacebook_token	: String;
		public var sError         	: String;

		public static inline var OPENED              	: String = 'OPENED';
		public static inline var CLOSED_LOGIN_FAILED 	: String = 'CLOSED_LOGIN_FAILED';
		public static inline var OPENED_TOKEN_UPDATED	: String = 'OPENED_TOKEN_UPDATED';
    }

    class HypFacebookDialogEvent extends HypFacebookEvent{

		public static inline var DIALOG_CANCELED	= "DIALOG_CANCELED";
		public static inline var DIALOG_ERROR   	= "DIALOG_ERROR";
		public static inline var DIALOG_SENT    	= "DIALOG_SENT";

		public var sPostID	: String;
    }

```
When you make a graph request, you get the raw String result from Facebook in a HypFacebookRequestEvent:

```haxe
    class HypFacebookRequestEvent extends HypFacebookEvent{

		public static inline var GRAPH_REQUEST_ERROR  	= "GRAPH_REQUEST_ERROR";
		public static inline var GRAPH_REQUEST_RESULTS	= "GRAPH_REQUEST_RESULTS";

		public var sResult   	: String;
		public var sGraphPath	: String;
	}

```
Quick reference
---------------

Present a request dialog:

```haxe
    var h = new Hash<String>( );
    h.set( "message" , "Test Request");
    h.set( "to" , "<fb user id>");
    fb.call( REQUEST_DIALOG( h ) );

```
Present a feed dialog:

```haxe
    var h = new Hash<String>( );
    h.set("name","Facebook extension for NME");
    h.set("caption","Build great social apps and get more installs with Haxe/NME.");
    h.set("description","The Facebook extension for NME makes it easier and faster to develop Facebook integrated apps build with Haxe");
    h.set("link","http://www.nme.io");
    h.set("picture","https://raw.github.com/fbsamples/ios-3.x-howtos/master/Images/iossdk_logo.png");
    fb.call( FEED_DIALOG( h ) );

```
Make a request to the Facebook Graph API:

```haxe
    var h = new Hash<String>( );
    h.set( "score", "42" );
    fb.call( GRAPH_REQUEST("/<fb user id>/scores", h ,POST) );

```
You can also present any [facebook dialog](https://developers.facebook.com/docs/reference/dialogs/) with:

```haxe
    fb.call( DIALOG( "<dialog name>", <hash of parameters> ) );

```
Made at [Hyperfiction](http://hyperfiction.fr)
--------------------
Developed by :
- [Louis Beltramo](https://github.com/louisbl) [@louisbl](https://twitter.com/louisbl)
- [Johann Martinache](https://github.com/shoebox) [@shoe_box](https://twitter.com/shoe_box)

License
-------
This work is under BSD simplified License.
