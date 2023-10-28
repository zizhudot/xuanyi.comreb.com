---
title: React Native Packaging and split
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

## 1. Packaging

react-native-cli provides bundle command, we can work with metro.config.js to configure the bundle parameters.

After installing react-native-cli, execute react-native bundle -h in the root directory of the rn project to get the following help message.

```shell
Options:
  --entry-file <path>                Path to the root JS file, either absolute or relative to JS root
  --platform [string]                Either "ios" or "android" (default: "ios")
  --transformer [string]             Specify a custom transformer to be used
  --dev [boolean]                    If false, warnings are disabled and the bundle is minified (default: true)
  --minify [boolean]                 Allows overriding whether bundle is minified. This defaults to false if dev is true, and true if dev is false. Disabling minification can be useful for speeding up production builds for testing purposes.
  --bundle-output <string>           File name where to store the resulting bundle, ex. /tmp/groups.bundle
  --bundle-encoding [string]         Encoding the bundle should be written in (https://nodejs.org/api/buffer.html#buffer_buffer). (default: "utf8")
  --max-workers [number]             Specifies the maximum number of workers the worker-pool will spawn for transforming files. This defaults to the number of the cores available on your machine.
  --sourcemap-output [string]        File name where to store the sourcemap file for resulting bundle, ex. /tmp/groups.map
  --sourcemap-sources-root [string]  Path to make sourcemap's sources entries relative to, ex. /root/dir
  --sourcemap-use-absolute-path      Report SourceMapURL using its full path
  --assets-dest [string]             Directory name where to store assets referenced in the bundle
  --reset-cache                      Removes cached files
  --read-global-cache                Try to fetch transformed JS code from the global cache, if configured.
  --config [string]                  Path to the CLI configuration file
```

A few parameters we need to use when packaging are:


--entry-file: the entry file, usually use index.js in the root directory as the entry file.

--platform: the platform for packaging, ios or android.

--dev: if or not in debug mode, default is debug mode, so you need to set it to false.

--minify: if or not the code should be compressed, when dev is true, minify will be false, when dev is false, minify will be true.

---bundle-output: path to store the packaged jsbundle.

--assets-dest: path to store the packaged resources, this path will generate the relative path of the resource files in the jsbundle, try to set it in the same directory as the jsbundle.

---sourcemap-output: path to output sourcemap, if not added, it will not output sourcemap.

--config: matroconfig configuration file, used to configure packaging information, can do some unpacking configuration.

## 2. Split bundle

By default, we can type a complete RN package directly with the following command.

```shell
react-native bundle --entry-file ./index.js --platform ios --dev false --bundle-output ./build/ios/main.jsbundle --assets-dest ./build/ios/assets
```

However, for a large project, many businesses are divided into business groups according to product lines or other scenarios, and it is not suitable for the whole application to hit a complete package. We can use something like Doodle Intelligence, each panel to create a RCTRootView and maintained by a separate RCTBridge form, the advantage is that there is no need to unpack, each panel business environment is independent, there is no variable pollution. The disadvantage is that each business package is very large, the download consumes resources, each panel creates a set of independent js runtime environment, it is not suitable for too many panels running at the same time.

We can also use the form of unpacking loading, each panel business is split into three parts.

1. RN framework part, this part of the RN framework for the core code, need to be loaded before the panel start to ensure that the subsequent loading logic is normal. This part will generally not change, you can follow the app version of the release iteration.

2. Public library part, such as some third-party dependencies, tuya-panel-kit and so on. This part can be released as a separate hot update version to fix the bugs in the public libraries.

3. Business part, this part is business code, which needs to be changed according to business requirements, and some non-public third-party library dependencies may be added.

For simplicity, we combine 1 and 2 into one module, which we call the base package. 3 as the business part is called the business package.

First we generate a configuration file for the base package, specifying which files need to be packaged into the base package and how the ids of each module are defined during the packaging process.

```js
const pathSep = require('path').sep;
function createModuleIdFactory() { // 获取每个模块的id如何定义
  const projectRootPath = __dirname;//获取命令行执行的目录，__dirname是nodejs提供的变量
  return path => {
    let name = '';
    if(path.indexOf('node_modules'+pathSep+'react-native'+pathSep+'Libraries'+pathSep)>0){
      name = path.substr(path.lastIndexOf(pathSep)+1);//这里是去除路径中的'node_modules/react-native/Libraries/‘之前（包括）的字符串，可以减少包大小，可有可无
    }else if(path.indexOf(projectRootPath)==0){
      name = path.substr(projectRootPath.length+1);//这里是取相对路径，不这么弄的话就会打出_user_smallnew_works_....这么长的路径，还会把计算机名打进去
    }
    return name; // 这里我们使用文件的相对路径作为每个模块的moduleId
  };
}
function processModuleFilter(module){// 过滤该module是否需要打包
  if (
         module.path.indexOf('node_modules') > -1 // node_modules下被引用到的文件需要打包
  ){
    return true;
  }
  return false;
}
function getRunModuleStatement(entryFilePath){
  return `__r('indexCore$js')`// 配置最后需要require执行的入口文件
}
module.exports = {
  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: false,
      },
    }),
  },
  serializer: {
    createModuleIdFactory:createModuleIdFactory,
    processModuleFilter:processModuleFilter,
    getRunModuleStatement:getRunModuleStatement
  }
};

```

Once the configuration is complete, we use it for packaging.

```shell
react-native bundle --config ./core.config.js  --entry-file ./indexCore.js --platform ${PLATFORM} --dev false --bundle-output ${OUTPUT_PATH}/${BIZ_FOLDER}.${PLATFORM}.js  --assets-dest ${OUTPUT_PATH}
```

After typing the RN package, we look at how the contents differ from the RN package when packaged directly.

```js
__d(function(g,r,i,a,m,e,d){'use strict';m.exports=r(d[0])},3,[4]);
__d(function(g,r,i,a,m,e,d){'use strict';m.exports=r(d[0])},"node_modules/react/index.js",["node_modules/react/cjs/react.production.min.js"]);
```

At this point, look at the packaged code, the original digital definition of the moduleId into the relative path to the file definition of the moduleId, is not a clearer and better understanding of it.

Similarly, we modify the processModuleFilter filtering rules to play out multiple business packages. Packaging and unpacking part of the realization is complete.