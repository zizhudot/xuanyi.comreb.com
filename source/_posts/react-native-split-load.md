---
title: React Native Split bundle and Loading
date: 2023-10-28 14:31:00
categories:
  - Front end
  - RN
tags:
  - RN
  - React Native
  - React
  - JavascriptCore
  - JSI
  - Split
  - loading
description: React Native Unpacking and Loading
---

## Understanding what js looks like in memory

Now we have a base package and multiple business packages, but how do we go about loading these RN packages? First we need to see what js looks like once it's loaded. We can view jscontext through the developer menu in safari or safari technology preview.

<image src="../assets/rn-6.png"/>

We found that after loading is completed is to read the specified file directory into memory, so what do we need to do to read this file into memory?

Option 1, the native side reads out the js file, passes it to the js side through a notification, and the js side executes the code through eval.

Option 2: Look at how RCTBridge loads the js code on the native side, and we load it in this way.

## Load the unpacked code into memory

No need to choose, surely option 2 is a bit more reliable. So we found these two methods.

```objective-c
- (void)executeSourceCode:(NSData *)sourceCode sync:(BOOL)sync;
- (void)executeSourceCode:(NSData *)sourceCode bundleUrl:(NSURL *)bundleUrl sync:(BOOL)sync;
```

What's the difference? As you can see from the parameters one doesn't set the path to the jsbundle and one provides a parameter to set that path. Let's choose the method with one more parameter to use.

So we use the js package we just packed out, and load it through these two methods. We get the following loading result.

<image src="../assets/rn-7.png"/>

We successfully loaded two RN packages into memory.

## Problems encountered

It went so well, how could it have gone so well? 

There are actually other considerations here:

### The base package has to be loaded first

The base package is the cornerstone of the business package, you must ensure that the base package loaded before you can load the business package, otherwise there will be a lot of exceptions. How to ensure that the base package is loaded? 

1. There is a js code loading notification mentioned above: RCTJavaScriptDidLoadNotification. 

2. By determining whether the isLoading property of the RCTBridge has changed to false.

```objective-c
 while (gRCTBridge.isLoading) {//侦听基础包是否加载完成 阻塞后续逻辑
      }
```

### Execute code in js thread

Code execution must be done in the js thread, otherwise there will be some inexplicable crashing problems.

```objective-c
[gRCTBridge.batchedBridge dispatchBlock:^{
      [gRCTBridge.batchedBridge executeSourceCode:source.data bundleUrl:bundUrl sync:YES];
 } queue:RCTJSThread];
```

### After loading the js code first, then create the RCTRootView

First make sure the js code is loaded before creating the RCTRootView, otherwise it will throw a require unknown module exception.

### Benefits of Split bundle

Having done all this, what exactly are the advantages of unpacking that make it worthwhile?

- Split business logic, reduce business coupling

- Split the business into multiple instances to load, to avoid the collapse of part of the business caused by the whole app is not available

- Reduce the waste of resources brought about by hot updates, split RN packages into basic packages, public packages and business packages, infrequently updated basic packages, public packages and business packages. package, the infrequently updated basic and public packages are excluded from the business package, which reduces the size of the business package and greatly reduces the bandwidth consumption when updating.