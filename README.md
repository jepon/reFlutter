[![workflow](https://github.com/ptswarm/reFlutter/actions/workflows/main.yml/badge.svg)](https://github.com/ptswarm/reFlutter/actions)

[![stars](https://img.shields.io/github/stars/ptswarm/reFlutter)](https://github.com/ptswarm/reFlutter/stargazers)
[![github_downloads](https://img.shields.io/github/downloads/ptswarm/reFlutter/total?label=downloads)](https://github.com/ptswarm/reFlutter/tags)

<p align="center"><img src="https://user-images.githubusercontent.com/87244850/135659542-22bb8496-bf26-4e25-b7c1-ffd8fc0cea10.png" width="80%"/></p>

#
This framework helps reverse engineer Flutter apps using patched version of Flutter library which is already compiled and ready for app repacking. There are changes made to snapshot deserialization process that allow you perform dynamic analysis in a convenient way.

Key features:
- `socket.cc` is patched for traffic monitoring and interception;
- `dart.cc` is modified to print classes, functions and some fields;
- contains minor changes for successfull compilation;
- if you would like to implement your own patches there is manual Flutter code change is supported using specially crafted `Dockerfile`
### Supported engines
- Android: arm64, arm32;
- IOS: arm64 (Unstable);
- Release: Stable, Beta
### Install
```
# Linux, Windows, MacOS
pip install reflutter

pip3 install reflutter
```
### Usage
```console
impact@f:~$ reflutter main.apk

Please enter your Burp Suite IP: <input_ip>

SnapshotHash: 8ee4ef7a67df9845fba331734198a953
The resulting apk file: ./release.RE.apk
Please sign the apk file

Configure Proxy in Burp Suite -> *:8083
Request Handling -> Support Invisible Proxying -> true

impact@f:~$ reflutter main.ipa
```
### Traffic interception
You need to specify the IP of your Burp Suite relative to your local network on which the device with the flutter application is located. Next, you must configure the Proxy in  `BurpSuite -> Listener Proxy -> Options tab`
- Add port: `8083`
- Bind to address: `All interfaces`
- Request handling: Support invisible proxying = `True`
<p align="center"><img src="https://user-images.githubusercontent.com/87244850/135753172-20489ef9-0759-432f-b2fa-220607e896b8.png" width="74%"/></p>

You don't need to install any certificates. On an Android device, you don't need root access. This also bypasses some of the flutter certificate pinning implementations.
## Android
The resulting apk must be aligned and signed. I am using [uber-apk-signer](https://github.com/patrickfav/uber-apk-signer/releases/tag/v1.2.1)
```java -jar uber-apk-signer.jar --allowResign -a release.RE.apk```.
To see what code is loaded through DartVM, you must run the application on the device. You need LogCat you can use Android Studio with reflutter keyword search or use adb logcat
### Output Example
```console
impact@f:~$ adb logcat -e reflutter | sed 's/.*DartVM//' >> reflutter.txt
```
<details>
<summary>code output</summary>

  
```dart
Library:'package:anyapp/navigation/DeepLinkImpl.dart' Class: Navigation extends Object {  

String DeepUrl = anyapp://evil.com/;

 Function 'Navigation.': constructor. (dynamic, dynamic, dynamic, dynamic) => NavigationInteractor { 
  
                   }
    
 Function 'initDeepLinkHandle':. (dynamic) => Future<void>* { 
  
                   }
    
 Function '_navigateDeepLink@547106886':. (dynamic, dynamic, {dynamic navigator}) => void { 

                   }
 
       }
 
Library:'package:anyapp/auth/navigation/AuthAccount.dart' Class: AuthAccount extends Account {

PlainNotificationToken* _instance = sentinel;
 
 Function 'getAuthToken':. (dynamic, dynamic, dynamic, dynamic) => Future<AccessToken*>* { 

                   }
  
 Function 'checkEmail':. (dynamic, dynamic) => Future<bool*>* { 
 
                   }

 Function 'validateRestoreCode':. (dynamic, dynamic, dynamic) => Future<bool*>* { 
 
                   }

 Function 'sendSmsRestorePassword':. (dynamic, dynamic) => Future<bool*>* { 

                   }
       }
```
</details>

## IOS
stub
### XCode
<p align="center"><img src="https://user-images.githubusercontent.com/87244850/135372439-822467e7-03db-4593-9063-09a2cec460c2.jpg" width="100%"/></p>

### To Do
- [ ] Display absolute code offset for functions;
- [x] Add socket patch;
- [ ] Extend engine support to Debug using Fork and [Github Actions](https://github.com/ptswarm/reFlutter/actions);
- [ ] Improve detection of `App.framework` and `libapp.so` inside zip archive

## Build Engine
Based on ```https://storage.googleapis.com/flutter_infra_release/flutter/<version_hash>/android-arm64-release/linux-x64.zip```
  
The engines are built using reflutter in [Github Actions](https://github.com/ptswarm/reFlutter/actions) to build the desired version, commits and hash snapshots are used from this [table](https://github.com/ptswarm/reFlutter/blob/main/enginehash.csv).
<details>
<summary>build</summary>
  
[![gif](https://user-images.githubusercontent.com/87244850/135758767-47b7d51f-8b6c-40b5-85aa-a13c5a94423a.gif)](https://github.com/ptswarm/reFlutter/actions)
  
</details>
