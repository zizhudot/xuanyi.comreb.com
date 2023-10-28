---
title: iPhone 15 Device Adaptation - Device identification
date: 2023-10-07 16:31:33
categories:
  - IOS development
tags:
  - iphoe
  - Objective-C
  - IOS
  - xCode
  - DNS
description: The way to identify iPhone 15 devices
---

## iPhone 15 device

```objective-c
   if ([platform isEqualToString:@"iPhone15,4"]) return @"iPhone 15";
   if ([platform isEqualToString:@"iPhone15,5"]) return @"iPhone 15 Plus";
   if ([platform isEqualToString:@"iPhone16,1"]) return @"iPhone 15 Pro";
   if ([platform isEqualToString:@"iPhone16,2"]) return @"iPhone 15 Pro Max";
```


```objective-c

// 需要#import "sys/utsname.h"

+ (NSString *)getDeviceIdentifier {

   **struct** utsname systemInfo;

   uname(&systemInfo);

   // 获取设备标识Identifier

   NSString *platform = [NSString stringWithCString:systemInfo.machine encoding:NSUTF8StringEncoding];

   

   // iPhone

   **if** ([platform isEqualToString:@"iPhone1,1"]) **return** @"iPhone 2G";

   **if** ([platform isEqualToString:@"iPhone1,2"]) **return** @"iPhone 3G";

   **if** ([platform isEqualToString:@"iPhone2,1"]) **return** @"iPhone 3GS";

   **if** ([platform isEqualToString:@"iPhone3,1"]) **return** @"iPhone 4";

   **if** ([platform isEqualToString:@"iPhone3,2"]) **return** @"iPhone 4";

   **if** ([platform isEqualToString:@"iPhone3,3"]) **return** @"iPhone 4";

   **if** ([platform isEqualToString:@"iPhone4,1"]) **return** @"iPhone 4S";

   **if** ([platform isEqualToString:@"iPhone5,1"]) **return** @"iPhone 5";

   **if** ([platform isEqualToString:@"iPhone5,2"]) **return** @"iPhone 5";

   **if** ([platform isEqualToString:@"iPhone5,3"]) **return** @"iPhone 5c";

   **if** ([platform isEqualToString:@"iPhone5,4"]) **return** @"iPhone 5c";

   **if** ([platform isEqualToString:@"iPhone6,1"]) **return** @"iPhone 5s";

   **if** ([platform isEqualToString:@"iPhone6,2"]) **return** @"iPhone 5s";

   **if** ([platform isEqualToString:@"iPhone7,1"]) **return** @"iPhone 6 Plus";

   **if** ([platform isEqualToString:@"iPhone7,2"]) **return** @"iPhone 6";

   **if** ([platform isEqualToString:@"iPhone8,1"]) **return** @"iPhone 6s";

   **if** ([platform isEqualToString:@"iPhone8,2"]) **return** @"iPhone 6s Plus";

   **if** ([platform isEqualToString:@"iPhone8,4"]) **return** @"iPhone SE";

   **if** ([platform isEqualToString:@"iPhone9,1"]) **return** @"iPhone 7";

   **if** ([platform isEqualToString:@"iPhone9,2"]) **return** @"iPhone 7 Plus";

   **if** ([platform isEqualToString:@"iPhone10,1"]) **return** @"iPhone 8";

   **if** ([platform isEqualToString:@"iPhone10,4"]) **return** @"iPhone 8";

   **if** ([platform isEqualToString:@"iPhone10,2"]) **return** @"iPhone 8 Plus";

   **if** ([platform isEqualToString:@"iPhone10,5"]) **return** @"iPhone 8 Plus";

   **if** ([platform isEqualToString:@"iPhone10,3"]) **return** @"iPhone X";

   **if** ([platform isEqualToString:@"iPhone10,6"]) **return** @"iPhone X";

   **if** ([platform isEqualToString:@"iPhone11,2"]) **return** @"iPhone XS";

   **if** ([platform isEqualToString:@"iPhone11,6"]) **return** @"iPhone XS MAX";

   **if** ([platform isEqualToString:@"iPhone11,8"]) **return** @"iPhone XR";

   **if** ([platform isEqualToString:@"iPhone12,1"]) **return** @"iPhone 11";

   **if** ([platform isEqualToString:@"iPhone12,3"]) **return** @"iPhone 11 Pro";

   **if** ([platform isEqualToString:@"iPhone12,5"]) **return** @"iPhone 11 Pro Max";

   **if** ([platform isEqualToString:@"iPhone12,8"]) **return** @"iPhone SE (2nd generation)";

   **if** ([platform isEqualToString:@"iPhone13,1"]) **return** @"iPhone 12 mini";

   **if** ([platform isEqualToString:@"iPhone13,2"]) **return** @"iPhone 12";

   **if** ([platform isEqualToString:@"iPhone13,3"]) **return** @"iPhone 12 Pro";

   **if** ([platform isEqualToString:@"iPhone13,4"]) **return** @"iPhone 12 Pro Max";

   **if** ([platform isEqualToString:@"iPhone14,4"]) **return** @"iPhone 13 mini";

   **if** ([platform isEqualToString:@"iPhone14,5"]) **return** @"iPhone 13";

   **if** ([platform isEqualToString:@"iPhone14,2"]) **return** @"iPhone 13 Pro";

   **if** ([platform isEqualToString:@"iPhone14,3"]) **return** @"iPhone 13 Pro Max";

   **if** ([platform isEqualToString:@"iPhone14,6"]) **return** @"iPhone SE (3rd generation)";

   **if** ([platform isEqualToString:@"iPhone14,7"]) **return** @"iPhone 14";

   **if** ([platform isEqualToString:@"iPhone14,8"]) **return** @"iPhone 14 Pro Plus";

   **if** ([platform isEqualToString:@"iPhone15,2"]) **return** @"iPhone 14 Pro";

   **if** ([platform isEqualToString:@"iPhone15,3"]) **return** @"iPhone 14 Pro Max";

   // iPhone 15 设备

   **if** ([platform isEqualToString:@"iPhone15,4"]) **return** @"iPhone 15";

   **if** ([platform isEqualToString:@"iPhone15,5"]) **return** @"iPhone 15 Plus";

   **if** ([platform isEqualToString:@"iPhone16,1"]) **return** @"iPhone 15 Pro";

   **if** ([platform isEqualToString:@"iPhone16,2"]) **return** @"iPhone 15 Pro Max";

   

   // iPod

   **if** ([platform isEqualToString:@"iPod1,1"])  **return** @"iPod Touch 1";

   **if** ([platform isEqualToString:@"iPod2,1"])  **return** @"iPod Touch 2";

   **if** ([platform isEqualToString:@"iPod3,1"])  **return** @"iPod Touch 3";

   **if** ([platform isEqualToString:@"iPod4,1"])  **return** @"iPod Touch 4";

   **if** ([platform isEqualToString:@"iPod5,1"])  **return** @"iPod Touch 5";

   **if** ([platform isEqualToString:@"iPod7,1"])  **return** @"iPod Touch 6";

   **if** ([platform isEqualToString:@"iPod9,1"])  **return** @"iPod Touch 7";

   

   // iPad

   **if** ([platform isEqualToString:@"iPad1,1"])  **return** @"iPad 1";

   **if** ([platform isEqualToString:@"iPad2,1"])  **return** @"iPad 2";

   **if** ([platform isEqualToString:@"iPad2,2"]) **return** @"iPad 2";

   **if** ([platform isEqualToString:@"iPad2,3"])  **return** @"iPad 2";

   **if** ([platform isEqualToString:@"iPad2,4"])  **return** @"iPad 2";

   **if** ([platform isEqualToString:@"iPad2,5"])  **return** @"iPad Mini 1";

   **if** ([platform isEqualToString:@"iPad2,6"])  **return** @"iPad Mini 1";

   **if** ([platform isEqualToString:@"iPad2,7"])  **return** @"iPad Mini 1";

   **if** ([platform isEqualToString:@"iPad3,1"])  **return** @"iPad 3";

   **if** ([platform isEqualToString:@"iPad3,2"])  **return** @"iPad 3";

   **if** ([platform isEqualToString:@"iPad3,3"])  **return** @"iPad 3";

   **if** ([platform isEqualToString:@"iPad3,4"])  **return** @"iPad 4";

   **if** ([platform isEqualToString:@"iPad3,5"])  **return** @"iPad 4";

   **if** ([platform isEqualToString:@"iPad3,6"])  **return** @"iPad 4";

   **if** ([platform isEqualToString:@"iPad4,1"])  **return** @"iPad Air";

   **if** ([platform isEqualToString:@"iPad4,2"])  **return** @"iPad Air";

   **if** ([platform isEqualToString:@"iPad4,3"])  **return** @"iPad Air";

   **if** ([platform isEqualToString:@"iPad4,4"])  **return** @"iPad Mini 2";

   **if** ([platform isEqualToString:@"iPad4,5"])  **return** @"iPad Mini 2";

   **if** ([platform isEqualToString:@"iPad4,6"])  **return** @"iPad Mini 2";

   **if** ([platform isEqualToString:@"iPad4,7"])  **return** @"iPad mini 3";

   **if** ([platform isEqualToString:@"iPad4,8"])  **return** @"iPad mini 3";

   **if** ([platform isEqualToString:@"iPad4,9"])  **return** @"iPad mini 3";

   **if** ([platform isEqualToString:@"iPad5,1"])  **return** @"iPad mini 4";

   **if** ([platform isEqualToString:@"iPad5,2"])  **return** @"iPad mini 4";

   **if** ([platform isEqualToString:@"iPad5,3"])  **return** @"iPad Air 2";

   **if** ([platform isEqualToString:@"iPad5,4"])  **return** @"iPad Air 2";

   **if** ([platform isEqualToString:@"iPad6,3"])  **return** @"iPad Pro (9.7-inch)";

   **if** ([platform isEqualToString:@"iPad6,4"])  **return** @"iPad Pro (9.7-inch)";

   **if** ([platform isEqualToString:@"iPad6,7"])  **return** @"iPad Pro (12.9-inch)";

   **if** ([platform isEqualToString:@"iPad6,8"])  **return** @"iPad Pro (12.9-inch)";

   **if** ([platform isEqualToString:@"iPad6,11"])  **return** @"iPad 5";

   **if** ([platform isEqualToString:@"iPad6,12"])  **return** @"iPad 5";

   **if** ([platform isEqualToString:@"iPad7,1"])  **return** @"iPad Pro 2(12.9-inch)";

   **if** ([platform isEqualToString:@"iPad7,2"])  **return** @"iPad Pro 2(12.9-inch)";

   **if** ([platform isEqualToString:@"iPad7,3"])  **return** @"iPad Pro (10.5-inch)";

   **if** ([platform isEqualToString:@"iPad7,4"])  **return** @"iPad Pro (10.5-inch)";

   **if** ([platform isEqualToString:@"iPad7,5"])  **return** @"iPad 6";

   **if** ([platform isEqualToString:@"iPad7,6"])  **return** @"iPad 6";

   **if** ([platform isEqualToString:@"iPad7,11"])  **return** @"iPad 7";

   **if** ([platform isEqualToString:@"iPad7,12"])  **return** @"iPad 7";

   **if** ([platform isEqualToString:@"iPad8,1"])  **return** @"iPad Pro (11-inch) ";

   **if** ([platform isEqualToString:@"iPad8,2"])  **return** @"iPad Pro (11-inch) ";

   **if** ([platform isEqualToString:@"iPad8,3"])  **return** @"iPad Pro (11-inch) ";

   **if** ([platform isEqualToString:@"iPad8,4"])  **return** @"iPad Pro (11-inch) ";

   **if** ([platform isEqualToString:@"iPad8,5"])  **return** @"iPad Pro 3 (12.9-inch) ";

   **if** ([platform isEqualToString:@"iPad8,6"])  **return** @"iPad Pro 3 (12.9-inch) ";

   **if** ([platform isEqualToString:@"iPad8,7"])  **return** @"iPad Pro 3 (12.9-inch) ";

   **if** ([platform isEqualToString:@"iPad8,8"])  **return** @"iPad Pro 3 (12.9-inch) ";

   **if** ([platform isEqualToString:@"iPad11,1"])  **return** @"iPad mini 5";

   **if** ([platform isEqualToString:@"iPad11,2"])  **return** @"iPad mini 5";

   **if** ([platform isEqualToString:@"iPad11,3"])  **return** @"iPad Air 3";

   **if** ([platform isEqualToString:@"iPad11,4"])  **return** @"iPad Air 3";

   

   // 其他

   **if** ([platform isEqualToString:@"i386"])   **return** @"iPhone Simulator";

   **if** ([platform isEqualToString:@"x86_64"])  **return** @"iPhone Simulator";

   

   **return** platform;

}
```