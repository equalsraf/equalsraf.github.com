title: 2 days with Android
tags: Android
date: 2011-01-14
icon: http://androidlovers.co.cc/wp-content/uploads/2011/03/Android-128.png
description: Trying to figure out how to develop with Android, ... my way.
nocomments:

After being annoyed with some show-stopping bugs at work, I decided to take two days
out of my life and tackle a major Bluetooth related BUG() we had in our Android application. 
Being the first time I tried to develop for android I was concerned I would have to use 
Eclipse, and loose my Vim-powered productivity. 

So here's a short primer on console based development for Android

## Installation

Being a happy openSUSE user, android packages were already available. Replace
the following with the appropriate (apt-get, yum) commands, or download the
SDK from the android website.


	$ sudo zypper install android-sdk
	$ kdesu android 

A nice enough GUI will open and you will be able to update the SDK and install
additional components.

## Starting adb 

To run code on the device you need to start the adb daemon. I 
do it as root, but you should probably setup some rules in udev to set
appropriate usb permissions.

	$ adb kill-server
	$ sudo adb start-server
	$ adb devices


## Lets create a project

To create a project in the current folder:

	$ android create project --target  1  --path . --package com.example.Test --activity MyAndroidAppActivity
	
The arguments:

 - Target(1) : Depends on your installation, check **android list**
 - Path(.) : The target path, in this case **.**, the current directory
 - Package(com.example.Test) : The Java package for your source code
 - Activity(MyAndroidAppActivity) : The name of the main activity class


## Compiling and running code

Android projects use **ant** to build the source and install it into the device. 
The following commands will compile, install and run the application in a plugged
Android device:

	$ ant compile
	$ ant install
	$ adb shell 'am start -n com.example.Test/.MyAndroidAppActivity'

Sometimes it is also useful to remove an application from the phone:

	$ adb uninstall com.example.Test


## Logging and Debugging

One of my favourite parts so far in developing for android is the debugging
logs. You can see a live log of what is happening in your device:

	$ adb logcat
	
or if you want to restrict it to a single application and log level:

	$ adb logcat -s MyActivity:V

If you are like me you'll want to keep track of stacktraces on the log.
Here's a treat:

	:::java
	public void logTrace(StackTraceElement[] trace) {
	    int i;
	    for (i=0; i<trace.length; i++) {
		 Log.v(this.getClass().getName(), trace[i].toString());          
	    }
	}
	...
	    logTrace(e.getStackTrace());


## A brief Conclusion

All in all not a bad development environment for the console. I admit I didn't
handle enough GUI development to miss the designer in eclipse, but still with 3 console
commands you are mostly ready to run your own app on the device. Which is more than can be 
said for several other development environments.

While the development environment was fine, I was pissed at the Java APIs. It
seems Bluetooth support in Android *sucks*, no Secure Simple Pairing, little to no control
over Bluetooth sockets other than the ability to *close* them, and the wretched 
*createRfcommSocketToServiceRecord(UUID)* method that does not seem to work.

Finally, I also took an hour to peek at native development with the android NDK, and I
wasn't particularly pleased about it, it has but the most basic libraries (libc, libz, etc), just
enough to optimise some heavy duty computation but useless for almost anything else. Things
like libbluetooth were sorely missed.

Not a bad way to spend two days, I actually got a lot done, which is lot to say about any 
first time use of any development environment. I like some of the concepts behind
Android development (Activities, AsyncTask), others not so much (Dialogs :S).


### PS: some extra bits I picked up later

If for any reason you lost you build.xml, just ask adb to rebuild it.

	$ android update project  --path . --target <TARGET>

To open a browser from the adb shell:

	$ am start -a android.intent.action.VIEW -n com.android.browser/.BrowserActivity http://www.raf.im


## References

Some additional tutorials on console powered development for android.

 - http://developer.android.com/guide/developing/other-ide.html
 - http://incise.org/android-development-on-the-command-line.html
 - http://mobile.tutsplus.com/tutorials/android/ndk-tutorial/
 - http://developer.android.com/
 - http://www.droiddraw.org/
