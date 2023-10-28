---
title: React Native Knowledge Points Explained
date: 2023-07-17 20:00:00
categories:
  - Front end
  - RN
tags:
  - RN
  - React Native
  - React
  - JavascriptCore
  - JSI
description: React Native Knowledge Points Explained
---

## 1. React Native 基本概念

### 1.1 JavascriptCore(JSC)

To ensure that the RN code runs, the first thing you need is a set of runtime environment for the JS code, which is JavascriptCore, but when debugging in chrome, since the JS code is all executed in chrome's V8 engine, it leads to differences between some of the code in debug mode and non-debug mode.

For example:

 1. Some date functions are not implemented in ios, for example, in IOS it is not possible to convert the date 2021-08-26 to Date object, but you need to replace "-" with "/" and then do the conversion, but you will not have this problem when debugging in chrome. 

 2. Android does not report an error if there is an illegal white space character that is not under the Text tag in a debug situation, but will report a red screen in a non-debug environment. Therefore, you need to understand these differences to avoid such situations.

About the details of JavascriptCore , you can refer to the [official document](https://trac.webkit.org/wiki/JavaScriptCore) , which describes in detail the components of the JSC and the role of each part .

### 1.2 JSI(Javascript Interface)

JSI is a lightweight c++ library, through jsi, you can realize js directly to the c++ layer object and method calls. It is an intermediate adaptation layer that can run on a variety of js engines. with jsi, it makes the RN framework not only run based on JSC, but also use V8 or hermes engine. jsi was introduced in 2018 when facebook refactored the RN framework, and after its introduction, the architecture of the RN also underwent a major change, and the performance was greatly improved.

### 1.3 jsbundle

Once we have the JS runtime, we need to load the executable code into the app. jsbundle is the JS code we need.

After the business development is completed, we will package the code with the package command provided by react-native-cli. The package script will encode the code we developed and generate a compressed bundle, such as a main.bundle js package for each panel program, and the dependent resources will be copied to the corresponding folder according to the path. folder according to the corresponding path.

#### a. Environmental variables and method definitions

The first line of the jsbundle defines the runtime environment variable, which is used to indicate that the node environment you are running on is in production and to record when the script was started.

```JS
var __DEV__=false,__BUNDLE_START_TIME__=this.nativePerformanceNow?nativePerformanceNow():Date.now(),process=this.process||{};process.env=process.env||{};process.env.NODE_ENV="production";
```

Lines 2 through 10 define global methods such as __d, __c, __r, setGlobalHandler, reportFatalError, etc., which are the basic methods for starting the RN environment.

#### b. ReactNative framework and business code definition

On line 11, we start to enter the React Native framework, third-party libraries, and personal code definition section, which defines the methods and variables in the code through the __d method defined on the second line.

The __d method accepts three parameters:

The first parameter indicates the definition of the module (typically the part of a file that is exported via export default). That is, the logic of the code in a particular code file written by us or a third-party developer.

The second parameter indicates the moduleId of the module, which needs to be referenced by this id when other modules make references to the module. The value can be a number or a string, but to ensure that each module id is unique. By default, the packaging system defines the id as an incremental number.

The third parameter represents the module's dependencies on other modules, and is an array where each number in the array represents a dependent module.

```JS
__d(function(g,r,i,a,m,e,d){var t=r(d[0]),n=t(r(d[1]));t(r(d[2]));r(d[3]);var l=r(d[4]),o=t(r(d[5])),s=r(d[6]),u=t(r(d[7])),p=t(r(d[8]));for(var f in l.TextInput.defaultProps=(0,n.default)({},l.TextInput.defaultProps,{allowFontScaling:!1}),l.Text.defaultProps=(0,n.default)({},l.Text.defaultProps,{allowFontScaling:!1}),console.disableYellowBox=!0,l.UIManager)l.UIManager.hasOwnProperty(f)&&l.UIManager[f]&&l.UIManager[f].directEventTypes&&(l.UIManager[f].directEventTypes.onGestureHandlerEvent={registrationName:"onGestureHandlerEvent"},l.UIManager[f].directEventTypes.onGestureHandlerStateChange={registrationName:"onGestureHandlerStateChange"});r(d[9]),r(d[10]),r(d[11]),g.userStore=(0,s.createStore)(u.default,(0,s.applyMiddleware)(p.default)),o.default.hide()},0,[1,2,3,6,18,416,417,420,422,423,656,727]);
__d(function(g,r,i,a,m,e,d){m.exports=function(n){return n&&n.__esModule?n:{default:n}}},1,[]);
__d(function(g,r,i,a,m,e,d){function t(){return m.exports=t=Object.assign||function(t){for(var n=1;n<arguments.length;n++){var o=arguments[n];for(var p in o)Object.prototype.hasOwnProperty.call(o,p)&&(t[p]=o[p])}return t},t.apply(this,arguments)}m.exports=t},2,[]);
__d(function(g,r,i,a,m,e,d){'use strict';m.exports=r(d[0])},3,[4]);
```

#### c. Citation and activation of entrances

Only defining it is not enough to make our RN application run, if it is going to run, we need to reference our entry module, so the __r method is used here.

The __r method accepts a parameter which is the id of the module to be referenced, if the module is not initialized, it tries to load and initialize the module, if the module is not found, it throws the error "Requiring unkonwn module 'xxx'".

```JS
__r(104);
__r(0)
```

### 1.4 RCTBridge/ReactBridge 

JS code and JS runtime are ready, how can we run these codes?

Have done or understand the RN development students know that in the RN development there is a Bridge concept is very important, it is in the js end and the native end of the role of the bridge, is the js and native communication and interaction with the basis of the entire RN lifecycle, it is mainly responsible for the following tasks.

a. Create the RN runtime

b. executing js code, loading the jsbundle and executing it

c. Maintaining dual-end communication between js and native

d. Maintaining export method tables and mapping relationships

### 1.5 RCTRootView/RCTShadowView

JS code, runtime, JS execution object are ready, we still need a view container to render the interface drawn in React.

At this point we need RCTRootView, RCTRootView as the root container of the RN, play a function of carrying all the sub-views, but the interface of React is not loaded directly to the RCTRootView, it has a layer of sub-view RCTRootContentView, which is the object that directly carries the view.

What is RCTShadowView? RCTShadowView is a mirror of the RCT view tree, similar to the virtual dom in React, it is responsible for maintaining the state of each view instance, when a change occurs on the js side, it will first be collected by the RCTShadowView to calculate the value of the change, and then synchronize the value to the corresponding view to be updated after the data processing is complete.

### 1.6 RCTUIManager

The view container is also there, then the interface so many views, so many instances of components, by whom to manage it?

UIManager takes on the responsibility of managing native views and passing live events, view instances caused by the native side are managed by UIManager, adding a unique tag as a key value for each view instance when creating the view, so that when you need to manipulate the view on the js side, you only need to pass the tag and parameters to UIManager, and you can locate a specific view instances.

### 1.7 RCTBridgeModule

js end and native end of how to communicate it , we should be how to define their own methods to let js call it?RN framework provides a protocol RCTBridgeModule, the implementation of this protocol can be realized on both ends of the communication.

RCTBridge and RCTUIManager both implement the RCTBridgeModule protocol, the RN environment will start scanning all the classes that implement the protocol to generate a table, the native side and JS side will save this table, it is a mapping table, with this mapping table you can let the two sides in the call method can be accurately found corresponding to the implementation.

### 1.8 MessageQueue

The communication bridge is not enough to support asynchronous operation, if all communication and UI rendering are executed synchronously, there will be a big performance bottleneck. Therefore, we need to introduce MessageQueue as a communication pool, all communication and interaction events are thrown into the pool, and then through the rules to read the pool to refresh the MessageQueue is mainly responsible for asynchronous event interaction notification tasks. This is also the reason why when calling native methods, the native side does not take effect immediately, for example, when locking the landscape operation, after calling the method of rotating to landscape, you need to do a delayed operation before locking the rotation.

```JS
RCTOrientationManager.lockOrientation('landscape-left');
setTimeout(() => {
    RCTOrientationManager.shouldAutorotate(false);
}, 200);
```

## 2. Operation principle of react native

React Native is mainly divided into two parts: one is React, that is, the JSX layer to realize the view and business logic. The second is Native, that is, the native end to take the logic implemented in the js layer for the interface rendering. The reason why these two parts can realize interoperability, rely on jsc (javascriptCore), jsc can execute js code, and through the analysis of js code implemented in the view mapped to the corresponding native components, on-demand execution of js logic code. It is because of the existence of jsc, so that we can write native applications through the js code. But jsc alone can't run an RN application properly, it relies on a number of other implementations.

### 2.1 RN Overall Architecture

In the jsi subsection we mentioned that the architecture of RNs has changed considerably due to the introduction of jsi.

#### 2.1.1 Current version of the architecture

We can divide the logic into three parts: JS thread, UI thread (main thread) and Shadow thread. The Shadow thread is mainly responsible for the calculation of the ShadowView update mentioned above, this part of the operation is done by the c++ layer of the yoga framework, and after the calculation is completed, the data will be handed over to the main thread to refresh the real view.

When React (js layer) needs to update the interface or call the native interface, it needs to convert the call parameters to JSON strings through the bridge, and then pass the JSON data to the native layer through the bridge, and then the native layer parses the JSON data to find the corresponding method or view to perform the operation, so it is not possible to realize the direct call between the js layer and the native layer, and meanwhile, the data cannot be shared among the three threads in data communication, so it is not possible to share the data among the three threads. At the same time, in the data communication between the three threads can not share the data, only to save a copy of their own, each maintained by the three threads in the communication can only be asynchronous call.

NativeModules need to be loaded at startup, and in the native end and js end services to maintain an object to ensure that in the js end call can correctly find the corresponding method, this part of the operation in the startup process will take up more resources, and will load a lot of unused resources.

Currently we use the 0.59.10 version of the RN framework, although the jsi architecture has been introduced, but in fact not used in the communication, communication is still realized through the bridge.

<image src="../assets/rn-1.png" />

#### 2.1.2 New version architecture

Compared to the current version of the architecture, the new version introduces the concept of jsi and fabric, jsi implements the js layer and the native layer of the inter-call , fabric will replace the UIManager , contains the renderer and shadow thread , and after the introduction of jsi , the realization of multiple threads before the data sharing , no longer need to use the json format to pass each other the data , and to retain a copy .

<image src="../assets/rn-2.png" />

The new version of the architecture and still use three threads for parallel processing , but all three threads can access the data in the js thread , NativeModules introduced TurboModules technology also , and then in the startup of all the load , but in the use of on-demand loading . At the same time the new architecture also introduces the CodeGen technology , according to the definition of types , automatically generate TurboModules native code , automatic compatibility between threads to communicate with each other .

The new version of the architecture has not yet been released, but has been iteratively used in facebook's internal applications, and it is expected that a major update will be released in the second half of the year. Reference: [https://reactnative.dev/blog/2021/08/19/h2-2021](https://reactnative.dev/blog/2021/08/19/h2-2021)

### 2.2 RN startup logic

Having understood the basic architecture of RN, we also need to understand how the startup process of the RN architecture of the current architecture works. Its overall logic can be simplified as the following process:

<image src="../assets/rn-3.png" />

#### 2.2.1 Creating the RCTRootView

As mentioned above, RCTRootView is a native container for RN interface, and generally for unpacked RN apps, RCTRootView can be created as the root view of the app when the app is launched.

We generally use the -(instancetype)initWithBundleURL:moduleName:initialProperties:launchOptions: method to create the RCTRootView. four parameters are passed to the url of the jsbundle, the name of the application to be launched (in JS, this is done through the app name (the name of the root component registered in JS via AppRegistry.registerComponent); initialization parameters (the props of the root component will be passed as startup parameters); app startup parameters (generally don't need to care about); RCTBridge will be created when creating RCTRootView using this method to maintain the whole RCTBridge will be created when RCTRootView is created using this method to maintain the entire life cycle of the RN application.

```js
   [[RCTRootView alloc] initWithBundleURL:[NSURL fileURLWithPath:panelPath] moduleName:@"Demo" initialProperties:initialProps launchOptions:launchOptions];
```

#### 2.2.2 Creating the RCTBridge

RCTBridge is an important object to maintain the life cycle of RN, we can also extract the required parameters, required methods, etc. in RCTBridge to you. Therefore, we usually use more to create an RCTBridge first and then create RCTRootView through the RCTBridge to initialize the RN application.

```objective-c
  RCTBridge *bridge = [[RCTBridge alloc] initWithDelegate:self launchOptions:launchOptions];
//或  
  RCTBridge *bridge = [[RCTBridge alloc] initWithBundleURL:[[NSBundle mainBundle]	URLForResource:@"main" withExtension:@"jsbundle"] moduleProvider:nil 	launchOptions:launchOptions];
  RCTRootView *rootView = [[RCTRootView alloc] initWithBridge:bridge
                                                   moduleName:@"Demo"

                                            initialProperties:nil];
- (NSURL *)sourceURLForBridge:(RCTBridge *)bridge
{
#if DEBUG
  return [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index" fallbackResource:nil];
#else
  return [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
#endif
}
```

The moduleProvider can be used to configure which NativeModules the bridge can access, which can be used when unpacking application control permissions.

#### 2.2.3 RCTCxxBridge

When RCTBridge is initialized, it saves the bundleUrl that was set at the time of creation, and creates an instance of RCTCxxBridge called batchedBridge to initialize the RN environment.

```objective-c
  self.batchedBridge = [[bridgeClass alloc] initWithParentBridge:self];
  [self.batchedBridge start];

```

#### 2.2.4 Loading the RCTBridgeModule

The batchedBridge startup first sends a notification that it will be loaded, after which it creates a js thread for thread initialization, and then registers the NativeModules.

```objective-c
 RCT_PROFILE_BEGIN_EVENT(RCTProfileTagAlways, @"-[RCTCxxBridge start]", nil);


  [[NSNotificationCenter defaultCenter] postNotificationName:RCTJavaScriptWillStartLoadingNotification
                                                      object:_parentBridge
                                                    userInfo:@{@"bridge" : self}];


  // Set up the JS thread early
  _jsThread = [[NSThread alloc] initWithTarget:[self class] selector:@selector(runRunLoop) object:nil];
  _jsThread.name = RCTJSThreadName;
  _jsThread.qualityOfService = NSOperationQualityOfServiceUserInteractive;
#if RCT_DEBUG
  _jsThread.stackSize *= 2;
#endif
  [_jsThread start];
...



  [self registerExtraModules];
  // Initialize all native modules that cannot be loaded lazily
  (void)[self _initializeModules:RCTGetModuleClasses() withDispatchGroup:prepareBridge lazilyDiscovered:NO];
  [self registerExtraLazyModules];

...
  dispatch_group_enter(prepareBridge);
  [self ensureOnJavaScriptThread:^{
    [weakSelf _initializeBridge:executorFactory];
    dispatch_group_leave(prepareBridge);
  }];


```

#### 2.2.5 Executing JS code

After NativeModules is loaded, it starts reading the jsbundle code into memory, and executes the js code when it's done.

```objective-c
  dispatch_group_enter(prepareBridge);
  __block NSData *sourceCode;
  [self
      loadSource:^(NSError *error, RCTSource *source) {
        if (error) {
          [weakSelf handleError:error];
        }


        sourceCode = source.data;
        dispatch_group_leave(prepareBridge);
      }
      onProgress:^(RCTLoadingProgress *progressData) {
#if (RCT_DEV | RCT_ENABLE_LOADING_VIEW) && __has_include(<React/RCTDevLoadingViewProtocol.h>)
        id<RCTDevLoadingViewProtocol> loadingView = [weakSelf moduleForName:@"DevLoadingView"
                                                      lazilyLoadIfNecessary:YES];
        [loadingView updateProgress:progressData];
#endif
      }];


  // Wait for both the modules and source code to have finished loading
  dispatch_group_notify(prepareBridge, dispatch_get_global_queue(QOS_CLASS_USER_INTERACTIVE, 0), ^{
    RCTCxxBridge *strongSelf = weakSelf;
    if (sourceCode && strongSelf.loading) {
      [strongSelf executeSourceCode:sourceCode sync:NO];
    }
  });
```

At this point the js code has been executed into memory and the root component has been registered to send the load completion notification, at this point RCTRootView can create RCTRootContentView.

#### 2.2.6 runApplication

Immediately after the RCTRootView is created the RCTRootContentView calls the AppRegistry.runApplication method to start loading the RN logic for rendering. At this point the RN logic process begins.

```objective-c
  _contentView = [[RCTRootContentView alloc] initWithFrame:self.bounds
                                                    bridge:bridge
                                                  reactTag:self.reactTag
                                            sizeFlexiblity:_sizeFlexibility];
  [self runApplication:bridge];


- (void)runApplication:(RCTBridge *)bridge
{
  NSString *moduleName = _moduleName ?: @"";
  NSDictionary *appParameters = @{
    @"rootTag" : _contentView.reactTag,
    @"initialProps" : _appProperties ?: @{},
  };


  RCTLogInfo(@"Running application %@ (%@)", moduleName, appParameters);
  [bridge enqueueJSCall:@"AppRegistry" method:@"runApplication" args:@[ moduleName, appParameters ] completion:NULL];
}
```

#### 2.2.7 Interface rendering on screen

After RCTRootView calls runApplicaton, the method call will send the business startup parameters in the form of messages to messageQueue(batchedBridge) on the js side through jscore (above mentioned messageQueue is a form of passively receiving data and actively refreshing it at regular intervals. By default, messageQueue will perform a flush operation every 5ms, and when it finds a new message during the flush, it will perform the logic according to the parameters of the message.) When runApplication is executed, it will use the components registered through AppRegistry.registerComponent to load the interface. At this time, the UIManager will create a dom configuration based on the components and levels in the dom tree, and pass it to the native RCTUIManager in the form of a json, the RCTUIManager will then create or refresh the Shadow based on the configuration, and then calculate the rendering parameters (width, height, position, etc.) of the components through the yoga, and pass the calculated configuration to the corresponding native view. Pass the calculated configuration to the corresponding native view, such as RCTView, RCTImage and other components, these components will be rendered to RCTRootContentView by layer to complete the rendering of the first screen.

<image src="../assets/rn-4.png" />


### 2.3 JSX and Native View Mapping Logic

It is mentioned above that the js-side uimanager needs to pass the configuration of each component to the native-side UIManager, which then handles the subsequent rendering process, so how are the configurations unified between the two ends of the UIManager?

In section 1.6, 1.7 we mentioned that RCTUIManager follows the RCTBridgeModule protocol, which allows the registration of modules and module methods.2.2.4 also mentioned that in the initialization process of RCTBridge, it will register the exported modules and methods, and after the registration is completed, the js side can get the references to the methods of these modules.

In the module registration, in fact, will be in the native side and js side at the same time to generate a configuration file, remoteModuleConfig, in the js side can be through the

In the js side, you can view this variable through __fbBatchedBridgeConfig.

The js side in the call method, according to this configuration file, will be located in the native side of the corresponding component for the corresponding event or configuration of the pass.

### 2.4 JS and Native Communication Logic

JS can trigger the native method by exporting the method in the module or component. The trigger is used here because the operation is asynchronous, so you can't get the return value directly from the native side, and you can only handle the return value through a callback or Promise. This also relies on the configuration file exported during module registration as mentioned in 2.3.

<image src="../assets/rn-5.png" />

### 2.4.2 Event Notification

The event passing mechanism is relatively simple, and both ends support sending notifications and registering listeners, we can register and send notifications through NativeAppEventEmitter, DeviceEventEmitter, NativeEventEmitter.

