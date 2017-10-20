# Overview 
In an effort to reduce startup latency for an iOS application I own - I'm trying to bootstrap Crashlytics on a background thread. This works ok except that it seems that Crashlytics is making use of a UIKit API which must be run on the main thread: 

```
Thread 2 Queue: com.apple.root.background-qos (concurrent)
#0  0x00000001050bf7ff in -[UIApplication statusBarOrientation] ()
#1  0x0000000103a53079 in -[CLSCrashReportingController captureInitialNotificationStates] ()
#2  0x0000000103a52f75 in -[CLSCrashReportingController setupStateNotifications] ()
#3  0x0000000103a5110d in -[CLSCrashReportingController installCrashReportingHandlers:] ()
#4  0x0000000103a5046c in -[CLSCrashReportingController startCrashReporterWithAPIKey:betaToken:profilingMark:report:] ()
#5  0x0000000103a50030 in -[CLSCrashReportingController startWithProfilingMark:betaToken:] ()
#6  0x0000000103a44aeb in __20-[Crashlytics start]_block_invoke ()
#7  0x000000010836c43c in _dispatch_client_callout ()
#8  0x000000010836d9c7 in dispatch_once_f ()
#9  0x0000000103a44868 in -[Crashlytics start] ()
#10 0x0000000103a44c9d in +[Crashlytics initializeIfNeeded] ()
#11 0x0000000103a792a7 in __15+[Fabric with:]_block_invoke ()
#12 0x000000010836c43c in _dispatch_client_callout ()
#13 0x000000010836d9c7 in dispatch_once_f ()
#14 0x0000000103a7908b in +[Fabric with:] ()
#15 0x0000000103a44e03 in +[Crashlytics startWithAPIKey:delegate:] ()
#16 0x0000000103a4252d in __57-[AppDelegate application:didFinishLaunchingWithOptions:]_block_invoke at /Users/mcueno/workspace/sandbox/AppleBugReports/CrashlyticsTest/CrashlyticsTest/AppDelegate.m:22
```

From production data, I've observed that the 90 percentile of starts (slowest 10%) takes about 250ms to bootstrap Crashlytics. Could be iPod touch devices or something equally slow.. The average was about 40ms.  

