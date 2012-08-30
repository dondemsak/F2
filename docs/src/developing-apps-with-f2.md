% Developing Apps with F2

<p class="lead">To understand how F2 apps work or to get started building apps, read through the documentation below. If you have not yet cloned the F2 repo on GitHub or downloaded the latest build, you should do that now by reading the [quick start instructions](https://github.com/OpenF2/F2#quick-start).</p>

## Overview

Defined very simply, a F2-capable app is a `javascript` file which defines your [app manifest](../../sdk/docs/classes/F2.AppManifest.html). Check out the [example apps on GitHub](https://github.com/OpenF2/F2/tree/master/sdk/examples/apps) to see some apps in action.

Technically speaking, a F2 app is one of two things:

<dl class="dl-horizontal">
	<dt>Presentation App</dt>
	<dd>A presentation app displays information to users in visible fashion (using HTML, CSS, and JavaScript).</dd>
	<dt>Data App</dt>
	<dd>A data app is a content feed available in industry-standard formats including JSON, JSONP, RSS or app developer-designed XML.</dd>
</dl>

For the purposes of this documentation and to get started, we'll focus on developing **presentation apps**. If you want to build a data app, [browse to data apps](#).

<div class="well well-small">
<h4>About Apps</h4>
<p>The term "app", in popular web parlance, is a program that runs on your smartphone or tablet. In the Open Financial Framework, apps are small web pages and consist of HTML, CSS, JavaScript and, of course, data. They are _not_ smartphone apps built for the Apple, Android or BlackBerry app stores. You could think of a F2 app as a module or widget or component.</p>
</div>

### Understanding F2 Apps

To develop an app for F2, there are a few things you need to first understand about apps themselves. Apps are useless without a container to run in and likewise containers aren't much to look at without apps. There are many [components of F2](components-of-the-framework.html), and apps and containers are only scratching the surface in terms of what is available to app developers today. From the concept of [app context](components-of-the-framework.html#app-context) to [creating a common look and feel](components-of-the-framework.html#creating-a-common-look-and-feel) across apps, apps are the cornerstone of F2-enabled multi-party web applications.

To help you get started, you will find a basic container in the [project repo on GitHub](https://github.com/OpenF2/F2/tree/master/sdk/examples/containers/HTML). Once you download and extract the archive, point your browser at:

`http://localhost/<your_extract_location>/OpenF2/sdk/examples/containers/HTML/`

<div class="alert alert-info"><strong>Note:</strong> The examples provided as part of the project repo demonstrate apps written in different languages (PHP, ASP, JavaScript). While it is not a requirement you have a web server configured on your computer, it will certainly enhance your F2 development experience.</div>

* * * *

## Apps and Containers

To understand F2 and the role of apps, you need to understand the role of the **container**.

![](../src/img/wwp_devices.png "Containers and Apps on desktop and mobile")

The F2 container is most simply described as the user interface and the location where all apps reside. More specifically, the container is a web page which is "aware" of its contents (the apps) and plays the role of a traffic cop managing context passing between F2 apps (if there are more than one). Further, the container can have any variation of intelligence on a wide spectrum which means it can provide data via web services to apps or simply host the F2 JavaScript SDK.

Each container provider, or individual person or company hosting a container, shall be responsible for including the [F2 JavaScript SDK](https://github.com/OpenF2/F2/blob/master/sdk/f2.min.js). Through the SDK, F2.js provides a consistent means for all app developers to load their apps on any container regardless of where it is hosted, who developed it, or what back-end stack it uses.

To get started working with or developing containers, browse to the [documentation for developing the container](developing-the-container.html).

* * * *

## F2 Apps

For an app to work with F2, it must have this basic structure&mdash;called the **App Manifest**&mdash;represented in [JSON](http://json.org):

```javascript
{
	"inlineScripts":[],	 
	"scripts":[],	 
	"styles":[],	 
	"apps":[{
			"data":{},
			"html":"",			
			"instanceId":"",
			"status":""
	}]
}
```

The App Manifest can be generated by the server-side code of your choice or be written-by-hand in your favorite text editor. In the [GitHub repository](http://www.github.com/OpenF2/F2), there are examples in JavaScript, PHP, Python and Classic ASP (JScript) to get you started.

When it's complete (using our examples below), the App Manifest looks like this:

```javascript
{
	"inlineScripts":["var foo = bar; doSomething();"], //discouraged, we'll explain later.
	"scripts":[
		"http://www.domain.com/js/app-logic.js",
		"http://www.domain.com/js/app-helper.js"
	],	 
	"styles":[
		"http://www.domain.com/css/base.css",
		"http://www.domain.com/css/ie7.css"
	],	 
	"apps":[{
			"data":{ 
				foo: "bar",
				value: 12345
			},
			"html":"%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E",		
			"instanceId":"b29ab39b-013a-45bc-b856-f058850f4b1e",
			"status":"good"
	}]
}
```

Let's break the App Manifest object down and look at each property (in reverse order to keep it fun).

### Apps

The `apps` property is an array of `AppContent` objects. Each `AppContent` object contains four properties: 

1. `html` 
2. `instanceId`
3. `data`
4. `status`

#### html

The `html` property contains the view of your app represented in [encoded](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/encodeURIComponent) HTML. While you can modify the way your app appears or functions within the container, the `html` property is what the container will show when it [registers your app](../../sdk/docs/classes/F2.html#method_registerApps) and displays its contents for the first time.

Example:

```javascript
"html": "%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E"
```

The _decoded_ version of the `html` example above is:

```html
<div class="sunrise">Hello world.</div>
```

#### instanceId

The `instanceId` property defines the unique runtime ID of the app. This can be an alpha-numeric reference number like a [GUID](http://en.wikipedia.org/wiki/Globally_unique_identifier) and is defined by the app developer. The main purpose of the `instanceId` is to allow an app to exist on a single container multiple times. 

The `instanceId` will be assigned by the container provider and passed to the app developer in the initial request for the App Manifest. This is how the app developer is able to use the `instanceId` when binding to the [`APPLICATION_LOAD`](../../sdk/docs/classes/F2.Constants.Events.html#property_APPLICATION_LOAD) event.

Example:

```javascript
"instanceId": "b29ab39b-013a-45bc-b856-f058850f4b1e"
```

The `instanceId` is also set as the ID of the outermost DOM element wrapping the app when it appears on the container.

Example `section` element wrapping a F2 app with the `instanceId` set as the value of the `id` attribute:

```html
<section class="f2-app" id="b29ab39b-013a-45bc-b856-f058850f4b1e">
	...
</section>
```

#### data

The `data` property is there to support the placement of arbitrary data needing to be passed along with the app. _This field is optional._

#### status

The `status` property allows app developers to communicate a server-side arbitrary status code to itself or to the container. _This field is optional._

### Styles

The `styles` property is an array of URLs represented as strings. The `styles` array refers to any CSS files needed by the app so it be displayed correctly on the container. The externally-referenced CSS files should be fully-qualified. [Read more about CSS and namespacing](#).

Example:

```javascript
"styles": [
	"http://www.domain.com/css/base.css",
	"http://www.domain.com/css/ie7.css"
]
```

### Scripts

The `scripts` property is an array of URLs represented as strings. The `scripts` array refers to any JavaScript files needed by the app so it be function correctly on the container. The externally-referenced JS files should be fully-qualified. [Read more about JavaScript and namespacing](#).

Example:

```javascript
"scripts": [
	"http://www.domain.com/js/app-logic.js",
	"http://www.domain.com/js/app-helper.js"
]
```

### Inline Scripts

The `inlineScripts` property is an array of strings. The `inlineScripts` array includes any JavaScript code needed by the app so it be function correctly on the container. The contents of the `inlineScripts` array will be evaluated as JavaScript when all `scripts` have finished loading. [Read more about JavaScript and namespacing](#).

Example:

```javascript
"inlineScripts": [
	"var foo = bar; doSomething();"
]
```

### Sample App Manifest

If we use the examples above, our app would look like this:

```javascript
{
	"inlineScripts":["var foo = bar; doSomething();"], //discouraged, we'll explain later.
	"scripts":[
		"http://www.domain.com/js/app-logic.js",
		"http://www.domain.com/js/app-helper.js"
	],	 
	"styles":[
		"http://www.domain.com/css/base.css",
		"http://www.domain.com/css/ie7.css"
	],	 
	"apps":[{
			"data":{ 
				foo: "bar",
				value: 12345
			},
			"html":"%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E",		
			"instanceId":"b29ab39b-013a-45bc-b856-f058850f4b1e",
			"status":"good"
	}]
}
```

## Developing Your F2 App 

Now that we've detailed the [F2 app](#f2-apps) and defined the [App Manifest](#sample-app-manifest), let's take a closer look at how to build _your_ app. We'll explain how to get a F2 AppId, what output format your app needs to support, how the contents of `AppContent.html` work, and the two hooks for adding form and function to your app: `scripts` and `styles`.

### F2 AppId

To develop a F2 app, you need a unique identifier called an **AppId**. This AppId will be unique to _your app_ in the entire open financial framework ecosystem. To guarantee uniqueness, we have provided an AppId generation service.

<a href="#" class="btn btn-large btn-primary">Get Your F2 AppId Now &raquo;</a>

### App HTML

Every F2 app has HTML. The only catch is that the HTML isn't provided by the app itself but rather _passed to the container_ via the App's AppManifest. But that's not a problem because we've [provided examples](https://github.com/OpenF2/F2/tree/master/sdk/examples/apps) to show you the way. Here are the steps for getting your app HTML into your `AppContent.html` property:

1. Develop the web page or module or widget or component or portlet that will be your app.
2. Take all the contents of it&mdash;that is, the HTML&mdash;and [encode it](http://opinionatedgeek.com/DotNet/Tools/HTMLEncode/Encode.aspx).
3. Put the encoded result in the `html` property of your `AppContent` object within your App Manifest file's `App` object.

**Huh?** Check out this example:

Step 1.

```html
<div class="sunrise">Hello world.</div>
```

Step 2. Encoded HTML.

```html
%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E
```

Step 3. App Manifest file. 

```javascript
{
	...
	"apps":[{
		"html": "%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E"
		...
	}]
}
```

### Scripts & Styles

Once your app is on the container, chances are you'll want it to actually do something. As an app developer, it is entirely up to you to write your own stylesheets and javascript code to add form and function to your app. F2's standardized App Manifest provides hooks for your CSS and scripts to get onto the container&mdash;just use the `scripts` and `styles` arrays detailed above in the [App Manifest](#sample-app-manifest).

<div class="alert alert-info"><i class="icon-warning-sign"></i> For details about CSS conflicts or javascript collisions once your app is on a container, read all about properly [namespacing your code in F2](#namespacing).</div>

### App Manifest Response

OK, so you know about F2 apps and you've got your own App Manifest file written. There's one final piece&mdash;the App Manifest response format. As part of F2, containers register apps&mdash;typically hosted on different domains&mdash;using JSONP. **This means F2 App Manifest files must provide a JSONP callback function.** (If you don't know what JSONP is or how it works, we recommend [reading what Remy Sharp has to say](http://remysharp.com/2007/10/08/what-is-jsonp/) about it.)

For security reasons, the App Manifest JSONP callback function must be a specific format. We have defined that using a combination of a stirng and your unique F2 AppId. The JSONP callback function name looks like this:

`F2_jsonpCallback_<AppID>`

When applied, the final App Manifest file looks like this example (where `123456789` is your AppId):

```javascript
F2_jsonpCallback_123456789({
	"inlineScripts":["var foo = bar; doSomething();"], //discouraged, we'll explain later.
	"scripts":[
		"http://www.domain.com/js/app-logic.js",
		"http://www.domain.com/js/app-helper.js"
	],	 
	"styles":[
		"http://www.domain.com/css/base.css",
		"http://www.domain.com/css/ie7.css"
	],	 
	"apps":[{
			"data":{ 
				foo: "bar",
				value: 12345
			},
			"html":"%3Cdiv%20class%3D%22sunrise%22%3EHello%20world.%3C%2Fdiv%3E",		
			"instanceId":"b29ab39b-013a-45bc-b856-f058850f4b1e",
			"status":"good"
	}]
})
```

<div class="alert">**Note:** the JSONP callback function name will _not_ be passed from the container using a traditional querystring parameter (HTTP GET), so you must configure this correctly for your app to appear on a container.</div>

## Namespacing


* * * *



* * * *

* * * *

## Stop reading here... ##

Each App must have a digital signature or unique identifier in the format of a GUID. This allows App Providers to register their App with the App Store and in turn the App Store can provide reporting metrics on App usage, purchases, etc. Additionally, having a unique ID allows the Container to interact with specific modules on the desktop using Context or in more simple cases using JavaScript’s document.getElementById() method.

Beyond a unique ID, Apps have other characteristics that define them within the Framework. The following are required attributes App developers must include in their Apps. The following data points should be represented within the presentation or data App:

```javascript
AppID – long (guid)
Name - string
Title - string
Context – object 
AcceptsContext – bool
DeveloperEmail - string
DeveloperURL – string
DeveloperCompanyName - string
```

Since Apps are comprised of mainly HTML, JavaScript and CSS, App development can be as complex as the App developer wishes with one significant limitation: an App cannot be allowed to negatively impact other Apps on the desktop. To prevent accidental impact, Apps are developed inside of JavaScript closure which means an App will not have any public methods and therefore is a closed cell. 

## Designing the App to Look Integrated with the Container

Design is an important first step in creating a new App. Using the Open Financial Framework’s upcoming design guidelines and App API, App Designers and Developers can take advantage of these available resources to develop Apps on their own schedules. The design guidelines will provide a common theme and offer a baseline for consistency between all Apps on the Container.

There is customization available and it will be imperative for App developers to follow Container-provided guidelines for consistency on a case-by-case basis. The ‘common theme’ provided by this Framework is not a template per se but rather a mechanism to facilitate faster development between numerous App developers.

## Loading an App on the Container 

Each App must “load” itself by calling a JavaScript method available in the SDK which will be hosted by the Container Provider. The arguments sent to “loadApp” are the App characteristics and any additional name/value data App Developers wish to include as part of their App’s Context.

```javascript
Container.loadApp({
	AppId: “1234-5678-9101-1121-3141-5161”,
	Name: “Acme Financials”,
	Title: “Acme Financials Module”,
	Context: {
		Symbol: “MSFT”,
		Name: “value”,
		List: [1,2,3,4]
	},
	AcceptsContext: true,
	DeveloperEmail: “dev@domain.com”,
	DeveloperURL: “http://domain.com”,
	DeveloperCompanyName: “Acme, Inc”
});
```

## Context

Each Container Provider shall be responsible for hosting the Container JavaScript SDK. This JavaScript framework provides a consistent means for all App Developers to load their Apps on any Container.

While Apps can have Context themselves, the responsibility for managing Context switching or Context passing falls on the Container. The Container assumes the role of a traffic cop – managing what data goes where. Using an Event Emitter, the Container can “listen” for events sent by Apps on configurable intervals and likewise Apps can listen for events sent by the Container. 

This is a sample of a Container sending Context to Apps. Firstly, the Container fires a “ContextUpdate” event.

```javascript
Container.on(“ContextUpdate”, { Symbol: “MSFT” }, function(ev,callback){
	console.log(“Context updated!”);
});
```

Apps are responsible for listening to the broadcasted “ContextUpdate” event. App developers can bind custom event handlers based on the emitted event. Using jQuery, a sample to refresh an app with a new symbol:

```javascript
$(“.myApp”).bind(“ContextUpdate”, function(ev,data){
	myApp.refresh(data.Symbol);
});
````

Likewise, Apps can send Context to the Container.

```javascript
App.on(“ContextUpdateToContainer”, { FullScreen: true }, function(ev, callback){
	console.log(“Context sent to Container!”);
});
```

The Container can then listen for App-emitted events.

```javascript
$(“#Container”).bind(“ContextUpdateToContainer”, function(ev,data){
	Container.showFullScreen();
});
```

Apps can also pass Context between Apps. If there are two or more Apps on a Container with similar Context and the ability to receive messages (yes, this is opt-out), Apps can communicate with each other.

For example, on your Container you have “App 1” which is a watch list app alongside “App 2” which is a snap quote app. Embedded within the Content in App 2 could be a button labeled “send to watch list”. Pressing that button would transmit the symbol of the stock currently being viewed in App 1 across to App 2 to be added to the watch list. Achieving that looks programmatically like this:

First, find the App you want to find the apps to which to send the Context to:

```javascript
var $apps = Container.getApps(bool canAcceptContext(true));
```

Secondly, emit an event from App 1:

```javascript
$apps.find(“App1”).on(
	“ContextToApp”, 
	{ Symbol”: “MSFT”, Action”: “ADD” }, 
	function(ev,data){
		myApp.refresh(data);
		}	
	);
```

And finally listen for that event within App 2:

```javascript
$apps.find(“App2”).bind(“ContextToApp”, function(ev,data){
	console.log(“Context received in App2!”);
});
```

## App Formats

Below is a sample request for an App named "Sample" in JSON format.

```metafont
http://www.domain.com/App/Factory/json?params=[{Id:"App1",Width:200,Name:"Value"}]
```

The "/json" format specifier in the above request could be safely omitted, as JSON is the default format. The widgets can be returned in a variety of formats as shown in the table below. Inclusion of a widget into an existing site varies based on the format requested.

The following output formats are required of any App:

-------------------------------------------------------------
Name 	Description
----	-----------
iframe  An inline JavaScript tag is inserted into the site. When the script is executed, an Iframe is written out to the page. The Iframe output format can be used for complex widgets or widgets that need to be self-contained.

json 	A JSON object containing the javascript, css, and widgets is inserted into the site via javascript already on the site.

jsonp 	Same as JSON but output is wrapped in a Callback function. Used for cross-domain communication and callbacks.

script 	An inline JavaScript tag tag is inserted into the site. Wen the script is executed, the javascript, css, and html for the widgets is written out to the page.
-------------------------------------------------------------

The JSON response format does not include type information. A description of the JSON format can be found at http://json.org. It will not be described in this Spec. A sample JSON response is shown below:

```javascript
{
	"InlineScripts":[],	 
	"Scripts":[],	 
	"Styles":[],	 
	"Apps":[
		{
			"Html":"\u003cdiv\u003eHello World!\u003c/div\u003e",
			"Data":
				{
					"Symbol":"AAPL"
				},				
			"Id":"Sample",				
			"Status":"SUCCESS"
		}]
}
```

The JSONP response format is similar, with the addition of a callback method name.

```javascript
mySampleCallbackName({
	"InlineScripts":[],	 
	"Scripts":[],	 
	"Styles":[],	 
	"Apps":[
		{
			"Html":"\u003cdiv\u003eHello World!\u003c/div\u003e",
			"Data":
				{
					"Symbol":"MSFT"
				},				
			"Id":"Sample",				
			"Status":"SUCCESS"
		}]
})
```

## Hosting an App

Since the Framework is web-based and it is a primary requirement of this Framework to simultaneously support multiple Apps from different providers, the following are truths:

* Anyone can technically host a Container provided they are willing to develop the infrastructure capable of supporting an app ecosystem which includes authentication, entitlements, the app store, cross-container communication (targeting version 1.3 spec), etc. See How to Develop a Container for details.
* Similarly, anyone can host an App. By definition, an App is simply a web page or web site which has a Container-accessible domain name.

The App Developer or App Provider needs to host their App on a publicly accessible Internet domain. The App should follow the "REST" model for web services. In simple terms, REST is a formal description of the HTTP protocol. Accessing a "REST" App is merely a matter of making a standard HTTP request to a defined resource.

An App can be accessed by an HTTP GET or HTTP POST request. Each request consists of a URI, and a URL encoded list of parameters in JSON. The URI consists of a hostname and base path, a method name, and an optional format specifier. The parameters are appended to the URI on the querystring.

## App Content

App Providers can determine which content they wish to make available within their App. It is recommended that content is focused on financial information; however, there is no limitation as such. Content can range from news to research to multimedia, and content should be presented using Progressive Enhancement development strategies. That is to say multimedia content, for example, should be shown plugin-free (using HTML5 video or audio elements) for capable browsers and fallback to Flash-based players for browsers that do not yet support HTML5 related technologies.

If App Providers embed URLs back to their own websites, URLs must be opened in a new window as to not interrupt the experience of someone using the Workspace. If authentication is required on App Providers’ site, this can be achieved with pass-through authentication using encrypted URLs as discussed in the Authentication API section of this specification.

## Single Sign-On

Providers participating in the Markit App Framework must modify their web sites' authentication mechanism to accept a customer’s authentication from a Container Provider without requiring the user to retype their Username and Password. With an authentication methodology in place between the Container Provider and App Provider, when a customer authenticates to a Provider’s App, the authentication credentials are passed to the Container Provider.  The authentication methodology is also suitable for the reverse, for authenticating a customer requesting content from an App Provider after selecting content from the App within the Container. Authentication information will be passed between App Providers and Container Providers in the form of encrypted URLs.

## Entitlements

User Entitlements are the responsibility of the App developer. Many apps will need to be decoupled from the content that they need. This would include apps like research aggregation, news filtering, streaming market data, etc. In order to enable an App to retrieve data from multiple, entitled, content providers in real-time, there will need to be an explicit and trusted mechanism of passing entitlements information between the Store, the data vendors, and the app developers.

Further details around entitlements will be forthcoming as this specification evolves.