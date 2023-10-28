---
title: React Native Hot Updates and Incremental Updates
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
  - Split
  - loading
  - bsdiff
  - Differential updating
description: React Native Hot Updates and Incremental Updates
---

## Hot update

### What is hot update

With the unpacking and loading logic, a hot update is really just downloading a new business package and replacing the old one.

### How to ensure that a hot updated version is available

But what if the hot update keeps crashing the business package?

Option 1: Release a bugfix version to fix the hot update. Prerequisite: R&D notices the online crash.

Option 2: Set up online rollback to previous version.

Option 3: Set up trial run mechanism locally, and set up three version directories locally: history version, trial run version and official version. The hot update version will be added to the trial run version first, and then moved to the normal version directory after the first successful run, and then run in the official version directory in the following days. If the trial run crashes continuously up to the maximum number of times (e.g. 2 times), then roll back the previous version (find the last runnable version in the history version directory) and record the failure of this hot update, and try the hot update again, if it also reaches the maximum number of times and still crashes, then give up this hot update version until the next hot update version is released.

### What's the time to update

There are many options for hot updating, such as updating all business packages when starting the app, polling regularly to update all business packages, pushing to update business packages, checking and updating the current business packages when starting the business, and so on.

#### Update all business packs when launching the app

The business logic is the simplest, after the start of the App first request a hot update interface to query whether there is a new version of the existing business package in the App, if there is a new version, then download the new version and wait for the run, if the business has not yet been launched after the start of the App, then use the new version to start the business package, if it has been launched, then use the new version of the business package next time you start.

Advantages: Update after startup, simple business logic processing, can ensure that in most scenarios when users enter the business to use the latest version of the business package.

Disadvantages: Increase the time consuming to start the app, when entering the business, the hot update may not be completed, and the old version of the business package is still used. For some users who do not often kill the process to exit the application is not friendly.

#### Timed polling to update all business packs

For applications that release hot updates more frequently, polling and downloading hot update packages at regular intervals is a common solution to ensure the business version update rate.

Advantages: Maximizes the reach of hot updates.

Disadvantages: The test of the hot update interface is large, and the peak availability of the request needs to be guaranteed by technical means. Timed requests have more invalid requests, wasting resources.

#### Push to update business packs

For most scenarios, push is a better way to update only when a version is available, without invalid requests and ensuring real-time version updates.

Advantages: Timely version update, no waste of resources caused by polling.

Disadvantages: The compatibility of Android push is poor, and there is no guarantee that users will turn on app push.

#### Update the current service package when starting a service

On-demand update, only detects and downloads the new version when the user starts the service, and uses the new version to enter the service after the update is completed.

Advantages: Ensure that users are always using the latest version.

Disadvantages: Every time you start the service, you have to check whether there is a new version update, there is a waste of resources; when there is a new version, the user needs to stop at the download page, and can enter the service only after the download is completed, so the user experience is poor.

## incremental update

The hot update feature is good enough for what we use, but there is still a waste of resources. Every time we update our business, we have to download a full size business package, but the changes to the business package may only be one line of code. So how do we go about reducing costs again.

Students who have done Android development should know that Android has a hot fix function, that is, the patch package, the contents of the current revision and the last comparison, split out the difference between the part of the generation of a patch package, the old version of the application to download the patch package, will be merged into the current version of the app, you get a new version of the app, most of the app stores have adopted a this technology.

We also have a diff method in git that allows us to see the differences between two files. In fact, incremental updates are similar to this git diff technique. In fact, incremental updating is similar to the git diff technique. It is to iterate through all the files in the business package that we have typed out, find out the files with differences, generate patch packages and compress them into a collection of patch packages of the corresponding version. When the app downloads the patch, it merges it with the corresponding version of the business package, and after the merge is complete, you get the new version of the business package.

How big is the difference between incremental update and full update?

The content of the business package before modification:

<image src="../assets/rn-8.png" />

Modified business package content:

<image src="../assets/rn-9.png" />

Modified full package size

<image src="../assets/rn-10.png" />

Modified incremental package size

<image src="../assets/rn-11.png" />

How big is a full volume package if you don't do unpacking?

<image src="../assets/rn-12.png" />

The demo project here only has a few simple pages, no third-party dependencies are imported. Imagine if you imported third-party dependencies and added some infrequently changing image resources, what would the ratio be.

## How to load a new RN package

Will downloading the RN package and loading the business directly with the new RN package take effect? Actually it doesn't.

As we all know, the dependencies we introduce in the file through import or require will eventually be converted into the form of reqiure, and require is defined in the metro. For example, after we modify and save a code file in an RN framework that doesn't support hot reload, we need to reload the whole project for it to take effect, this is because the require method has a caching policy.

We open the require.js file, we will find the following logic.

```js
  const module = modules[moduleIdReallyIsNumber];
  return module && module.isInitialized
    ? module.publicModule.exports
    : guardedLoadModule(moduleIdReallyIsNumber, module);

```

We can see from the logic that require will only read and load the corresponding code if the module with the corresponding moduleId has not been defined, but if it has been defined, it will go to the data in the cache. Therefore, if we hot update a business package, if we have started it once before, we will not use the logic in the new business package again. So what do we do?

Through the above code, we found that the module code will be cached in the modules variable, so when we exit the business, the relevant module clean up can not be? But what time to clean up?

Here we use a familiar and unfamiliar Api, AppRegistry. Open the AppRegistry document and we find an application uninstallation api, unmountApplicationComponentAtRootTag, which will be called when the application exits the uninstallation. This api will be called when the app exits and uninstalls. But what things to clean up, is not necessary for us to record, can not be all the modules empty it. We can see the runApplication method, which is the js method called by RCTRootView when it calls start, and it is the starting point of the application. It is the starting point of the application. We can save the modules and the names of the modules that the application depends on according to the entry parameter.

```js
const initRunApplication = AppRegistry.runApplication;
const initUnmountApplicationComponentAtRootTag = AppRegistry.unmountApplicationComponentAtRootTag;
AppRegistry.runApplication = (appKey, appParameters) => {
  const { rootTag, initialProps: { bundleName, bundleUrl, entry } } = appParameters;
  definedBizModules[rootTag] = { entry, prefix: `src/pages/${bundleName}/` };
  new SourceTransformer(bundleUrl, bundleName)
  initRunApplication(appKey, appParameters);
}
AppRegistry.unmountApplicationComponentAtRootTag = (rootTag) => {
  initUnmountApplicationComponentAtRootTag(rootTag);
  const { entry, prefix } = definedBizModules[rootTag];
  console.log('unmount', entry)
  global.__destroyModules(entry, prefix)
}

```

In order to keep the application running properly, we need to keep the original method and execute it as soon as our hook code finishes executing.

How global.__destroyModules is defined, this is where we need to modify some code in the require.

```js
 const destroyModules = function (dModule, prefix) {
    if (typeof dModule === 'string') {
      delete modules[dModule];
      Object.keys(modules).forEach((item,i)=>{
          if(item.startsWith(prefix)){
            delete modules[item];
          }
      })
    }
  };
  global.__destroyModules = destroyModules;
```

We'll clean up all the mods under the specified path, and the next time we go back in, they'll be loaded properly.
