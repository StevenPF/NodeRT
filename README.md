NodeRT
======
<H3>WinRT modules generator for node.js</H3>

NodeRT is a tool that automatically generates node.js Native add-on wrappers for <a href="http://en.wikipedia.org/wiki/Windows_Runtime">WinRT APIs</a>.

NodeRT automatically exposes Microsoft’s WinRT APIs to the node.js environment by generating node modules. This enables node.js developers to write code that consumes native Windows capabilities. The generated modules' APIs are (almost) the same as the <a href="http://msdn.microsoft.com/en-us/library/windows/apps/br211377.aspx">WinRT APIs listed in MSDN</a>.
NodeRT can be used to generate node.js modules both from command line (NodeRTCmd) and from its UI tool (NodeRTUI).

NodeRT is developed and released by a group of node.js enthusiasts at Microsoft.

Here is an example of using NodeRT <a href="http://msdn.microsoft.com/library/windows/apps/br225603">windows.devices.geolocation</a> module to retrieve the current location:

```javascript
var geolocation = require('windows.devices.geolocation');
var locator = new geolocation.Geolocator();

locator.getGeopositionAsync( function(err, res) {
  if (err) {
    console.error(err);
    return;
  }

  console.info('(', res.coordinate.longitude, ',',  res.coordinate.latitude, ')');
});
```

For more examples of what NodeRT can do, check out our <a href="/samples">samples section</a>.

----------
<H3>Documentation</H3>

<a href="#Prerequisites">NodeRT Prerequisites</a>

<a href="#GeneratingWithUI">Generating a NodeRT module using the UI</a>

<a href="#GeneratingWithCmd">Generating a NodeRT module using the cmd line interface</a>

<a href="#ConsumingNodeRT">Consuming a NodeRT module in node.js</a>

<a href="#License">License</a>

<a href="#Attributions">Attributions</a>

<a href="#Contribute">Contribute</a>

-----------
<a name="Prerequisites"></a> 
<H3>NodeRT Prerequisites</H3>
First, in order to use WinRT you must be running on a Windows environment that supports WinRT- meaning Windows 8.1, Windows 8, or Windows Server 2012.

In order to use NodeRT, make sure you have the following installed:<br>
* Visual Studio 2013, or <a href="http://www.microsoft.com/en-gb/download/details.aspx?id=40787">VS 2013 Express for Windows Desktop</a>, for generating Windows 8.1 compatible modules, or Visual Studio 2012 for generating Windows 8 compatible modules.<br>
* node.js (version > 10.*) - from <a href="www.nodejs.org">www.nodejs.org</a><br>
* node.js native development files - these files can be installed using the following cmd-line:
```
node-gyp install
```
(In order to make sure those files are installed - check if the following directory exists: 
```
c:\users\[your user name]\.node_gyp\[your node.js version]
```
  Another option is to download the node.js source code to your machine and build it. More information on this is available in the node.js site/github repository.<br>

Next, download the NodeRT binaries from <a href="https://github.com/NodeRT/NodeRT/raw/master/bin/NodeRT.zip">here</a>, or clone this repository to your machine and build the NodeRT solution using Visual Studio.

-----------
<a name="GeneratingWithUI"></a> 
<H3>Generating a NodeRT module using the UI</H3>
First, launch the UI tool by running NodeRTUI.exe:<br>

![Alt Windows.Devices.Geolocation NodeRT module contents](/doc/images/nodert_screenshot.png)

Then, follow this short list of steps in order to create a NodeRT module:<br>
* Choose a WinMD file: <br>
    - For Windows 8.1 SDK: <br>
    ```
    c:\Program Files (x86)\Windows Kits\8.1\References\CommonConfiguration\Neutral\Windows.winmd
    ```
    - For Windows 8.0 SDK: <br>
    ```
    c:\Program Files (x86)\Windows Kits\8.0\References\CommonConfiguration\Neutral\Windows.winmd
    ```
* Choose a namespace to generate from the list of namespaces.<br>
* Select whether you are generating a Windows 8.1 compatible module, using VS2013 or a Windows 8.0 compatible module using VS2012.<br>
* Choose the output directory in which the module will be created, or just stick with the default ones.
* You're good to go, hit the Generate & Build button! A message box with (hopefully) a success message should appear shortly.<br>

-----------
<a name="GeneratingWithCmd"></a> 
<H3>Generating a NodeRT module using the cmd line interface</H3>
NodeRT modules generation is available via a cmd-line interface using the NodeRTCmd tool.

An example of generating the Windows.Devices.Geolocation namespace from the Windows 8.1 Windows.winmd:
```
NodeRTCmd.exe --winmd "c:\Program Files (x86)\Windows Kits\8.1\References\CommonConfiguration\Neutral\Windows.winmd" --codegendir c:\NodeRT\codegen --outdir c:\NodeRT\output --namespace Windows.Devices.Geolocation
```
Note that omitting the --namespace option will generate all of the namespaces in the Winmd file.

The following is the list of options that the tool supports:
```
 --winmd [path]           File path to winmd file from which the module
                          will be generated

 --namespaces             Lists all of the namespaces in the winmd file
                          (only needs --winmd)

 --namespace [namespace]  The namespace to generate from the winmd when
                          not specified , all namespaces will be generated

 --outdir [path]          The output dir in which the compiled NodeRT module
                          will be created in

 --vs [Vs2012|Vs2013]     Optional, VS version to use, default is Vs2013
 
 --nodefgen               Optional, specifying this option will reult in
                          skipping the generation of TypeScript and
                          JavaScript definition files

 --nobuild                Optional, specifying this option will result in
                          skipping the build process for the NodeRT module

 --help                   Print this help screen

```

-----------
<a name="ConsumingNodeRT"></a> 
<H3>Consuming a NodeRT module in node.js</H3>

Requiring a generated NodeRT module is just like requiring any other node.js module - if for example, you've just generated Windows.Devices.Geolocation, copy the generated windows.devices.geolocation directory from the output folder to a node_modules folder near you (or use a full path), and run:
```javascript
var geolocation = require('windows.devices.geolocation');
```

If you are working in the node console (AKA REPL),
then entering <i>geolocation</i> will result in printing the contents of the namespace:

![Alt Windows.Devices.Geolocation NodeRT module contents](/doc/images/object_contents.png)

Creating a new WinRT object is done with the new operator. In order to inspect the method and properties of the object, you can print its prototype:
For example, creating a new Geolocator object in REPL:
```javascript
var locator = new geolocation.Geolocator();
//print the prototype
locator.__proto__
```
And the output will be:

![Alt Geolocator prototype contents](/doc/images/golocation__proto.png)

(Note that property values are fetched on the fly, and hence have undefined values when printing the prototype)

<b>Classes and fields naming</b>

We use the same convention used for WinRT javascript applications:

* Class/Enum names have the first letter in upper-case

* Class/Enum fields, that is properties, methods, and events, both member and static have the first letter in lower-case, the rest of the name is according to MSDN. 

* Enums are just javascript objects with keys corresponding to the enum fields and values to the enum fields numeric values.

<b>Properties</b>

Using Properties of an object is just straight-forward javascript, for example:
```javascript
locator.reportInterval = 2000;
console.info(locator.reportInterval);
```
<b>Synchronous methods</b>

Again, straight-forward javascript, just make the call with the appropriate arguments. If there are several WinRT overloads for the method, make the call with the right set of arguments and the correct overload of the method will be called:
```javascript
var xml = require('windows.data.xml.dom');
var xmlDoc = new xml.XmlDocument();
toastXml.loadXml('<node>some text here</node>');
```

<b>Asynchronous methods</b>

Each async method accepts the same variables as are listed in the MSDN, with the addition of a completion callback as the last argument.<br>
This callback will be called when the function has finished, and will receive an error as the first argument, and the result as the second argument:
```javascript
locator.getGeopositionAsync( function(err, res) {
  // result is of type geoposition
  if (err) {
    console.error(err);
    return;
  }

  console.info('(',res.coordinate.longitude, res.coordinate.latitude, ')');
});
```

If you wish to use the exact method signatures that are listed in the MSDN examples, you can use the nodert-promisize module (located in modules/nodert-promisize), or any other similar node.js library:

```javascript
var promisize = require('nodert-promisize').promisize;
var geolocation = promisize('windows.devices.geolocation');
var locator = new geolocation.Geolocator();
locator.getPositionAsync(filePath)
  .then(function(res) {
    console.info('(',res.coordinate.longitude,   res.coordinate.latitude, ')');
  }, 
  function(err) {
    console.error(err);
  }
);
```

<b>Events</b>

Registering to events is done using the class' <i>on</i> method (which is equivalent to <i>addListener</i>), which receives the event name (case insensitive) and the event handler function.
For example:
```javascript
var handler = function handler(sender, eventArgs) {
  console.info('status is:', eventArgs.status); 
};
locator.on('statusChanged', handler);
```
Unregistering from an event is done the same way, using the class's <i>off</i> or <i>removeListener</i> methods. 
Just make sure to store the event handler in order to be able to use it.
```javascript
// using same event handler as in the example above
locator.off('statusChanged', handler);
```

<b>Separation into namespaces and cross namespace usage</b>

Each NodeRT module represents a single namespace. <br>
For instance, windows.storage will have a NodeRT module, and windows.storage.streams will have another NodeRT module.<br>
The reason for this separation is strictly due to performance considerations.<br>
(We didn't want to have a huge NodeRT module that will cause the memory of node.js to blow up while most of the namespaces probably won't be used in each script).

This architecture means that in case you are using a NodeRT module which contains a function, property, or event which returns an object from another namespace, then you will need to require that namespace **before** calling that function/property/event.

For example:
```javascript
var capture = require('windows.media.capture');
// we also require this module in order to be able to access device controller properties
var devices = require('windows.media.devices');

var capture = new capture.MediaCapture();
capture.initializeAsync(function (err, res) {
  if (err) {
    return console.info(err);
  }
  
  // get the device controller, its type (VideoDeviceController) is defined in the 
  // windows.media.devices namespace -  so, we had to require that namespace as well
  var deviceController = capture.videoDeviceController;
  
  // we can now use the VideoDeviceController regularly
  deviceController.brightness.trySetValue(-1);
});
```

<b>Using WinRT streams in node.js</b>

In order to support the use of WinRT streams in node.js, we have created the <a href="/modules/nodert-streams">nodert-streams</a> module, which bridges between WinRT streams and node.js streams.

This bridge enable the conversion of WinRT streams to node.js streams, such that WinRT streams could be used just as regular node.js streams.

-----------
<a name="License"></a> 
<H3>License</H3>
NodeRT is released under the Apache 2.0 license. 
For more information, please take a look at the <a href="./LICENSE">license</a> file.

-----------
<a name="Attributions"></a> 
<H3>Attributions</H3>
In order to build NodeRT we used these 2 great libraries:
* RazorTemplates - https://github.com/volkovku/RazorTemplates
* RX.NET - https://github.com/Reactive-Extensions/Rx.NET/

-----------
<a name="Contribute"></a> 
<H3>Contribute</H3>
You are welcome to send us any bugs you may find, suggestions, or any other comments.
Before sending anything, please go over the repository issues list, just to make sure that it isn't already there.

You are more than welcome to fork this repository and send us a pull request if you feel that what you've done should be included. 