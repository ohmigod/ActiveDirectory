---
description: Android Apps Static Analysis
---

# Static Analysis

## Download APK

### adb

Use "adb" to download the APK to our PC. Other tools such as dex2jar/jd\_gui or mara/mobsf works as well.

```bash
#Install Wi-Fi ADB on the phone and on the PC. Then run from our PC:
adb connect ip:port #connect to mobile adb
adb devices #list devices
adb shell #open a shell
adb pull "/data/app/com...../base.apk" . #Download APK to current folder
unzip Appbase.apk #Extract APK files
```

### apk-downloader

Another way to download an Apk application directly from Google Play is to use [Evozi's Apk Downloader](https://apps.evozi.com/apk-downloader/) service. it's very simple, powerful and super practical:

* Search the Google Play Store for the app you want to download and copy its link.&#x20;
* Open the “Apk Downloader” website.&#x20;
* Paste the link you copied from Google Play and click on “Generate download link”.&#x20;
* A link will be generated for you to download the Apk directly from Google Play.&#x20;

## Analyze Code

### Converting APK into JAR

[dex2jar](https://github.com/pxb1988/dex2jar) enables converting an APK file into a JAR one so that the application’s code can be viewed. Then you can open it in a GUI using [jd-gui](https://github.com/java-decompiler/jd-gui).

```bash
sh d2j-dex2jar.sh -f ~/path/to/apk_to_decompile.apk -o /path/to/output
#The output file will be apk_to_decompile-dex2jar.jar

$ jd-gui #Then file > open > apk_to_decompile-dex2jar.jar
```

### MobSF

[Mobile Security Framework (MobSF)](https://github.com/MobSF/Mobile-Security-Framework-MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework capable of performing static and dynamic analysis.

```bash
#Launch using docker
docker run -p8000:8000 opensecurity/mobile-security-framework-mobsf
#Go to localhost:8000 and upload the APK
#Run static analysis checks and analyze the results
```

<figure><img src="../../.gitbook/assets/mobsf.gif" alt=""><figcaption></figcaption></figure>

### Mara

****[**MARA**](https://github.com/xtiankisutsa/MARA\_Framework) is a **M**obile **A**pplication **R**everse engineering and **A**nalysis Framework. It is a tool that puts together commonly used mobile application reverse engineering and analysis tools, to assist in testing mobile applications against the OWASP mobile security threats.&#x20;

```
./mara.sh -s /path/to/file.apk
```

<figure><img src="../../.gitbook/assets/mara.png" alt=""><figcaption></figcaption></figure>

### Search for juicy info

Once we have the source code, navigate to the root folder and search for secrets, URLs, IPs, endpoints, PII...

```bash
#APK files:
grep -ira "http:" ./*
grep -ira "https:" ./*
grep -ira "token" ./*
grep -ira "password" ./*
grep -ira "string" ./*
grep -ira "hash" ./*
grep -ira "access" ./*
grep -ira "user" ./*
grep -ira "admin" ./*
grep -ira "root" ./*
.
.
#Other files/directories that might contain secrets:
/data/data/<app>
/storage/emulated/0
/mnt/extSdCard
/mnt/external_sd
```

### App permissions

Every app project have an AndroidManifest.xml file at the root of the project source set. The manifest file describes essential information about the app to the Android build tools, the Android operating system, and Google Play.&#x20;

Review the permissions that the app needs in order to access protected parts of the system or other apps. It also declares any permissions that other apps must have if they want to access content from this app.

