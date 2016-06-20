# Progressive and Subcompact Books: Technical notes

> ***This is meant as living document. Feedback is appreciated and will be incorporated when appropriate. The idea is to use this and its sister philosophical document as the basis for a proof of concept application***

## What browsers to target?

The easiest answer is [Evergreen Browsers](http://eisenbergeffect.bluespire.com/evergreen-browsers/). But we need to think what that means beyond putting the list out. In my first, knee jerk reaction, the list is this:

* Chrome
* Opera
* Firefox 
* Edge

As Scott Hanselman [points out]([http://www.hanselman.com/blog/TheEvergreenWeb.aspx]) we have a responsibility as developers to keep our sites fresh as much as browser makers have a reponsibility to update their browsers in ways that won’t break “all of the web”. 

> In a world where we all write our websites with feature detection and (generally) gracefully degrade when features aren't around, things just work. But at the same time, it does make the Web itself a moving target.

Can we afford to run a minimum common denominator? According to Brian Kardell’s [support gaps table](https://rawgit.com/bkardell/gaps/master/gaps-caniuse-2-2013.html), out of the 134 features tracked in caniuse.com are supported by at least 3 evergreen browsers without having support in Internet Explorer 9 or earlier. 

[this table which uses data from webbrowsercompatability.com](https://rawgit.com/bkardell/gaps/master/gaps-caniuse-2-2013.html) shows 460 of the   properties/features that test for are available in most evergreen browsers but, again, pretty much none of them before IE9.  

From a developer’s point of view it’s an easy choice but from an end user’s perspective it’s not so simple.  In [The problem with evergreen browsers](https://fragdev.com/blog/the-problem-with-evergreen-browsers) we are reminded again about some of the dawbacks of browser compatibility. The biggest one is still the fact that some corporate IT groups block manual and automatic browser updates so their users cannot take advantage of the new evergreen features until IT decides that the risk is warranted and that their legacy applications will work with the latest version of all browsers. 

So how do we deal with this support nightmare?

Most of the web today works in a [progressive enhancement](http://alistapart.com/article/understandingprogressiveenhancement) paradigm. We start with the basic experience and then enhance the experience for browsers that can handle it. The opposite is [graceful degradation](http://zurb.com/word/graceful-degradation) where we provide the full experience as a starting point and degrade gracefully for browsers that don’t support the full package or which size is an issue. 

I’m choosing to work through graceful degradation. Starting with a full size experience we’ll explore how to make it work in smaller form factors without loosing sight that the evergreen browser is the primary experience for this particular use case. 

It is also important to realize that older browsers will still be able to see the content, just not in as rich a way as the target browsers can. 

## Building an HTML reading platform

There are are couple things I still struggle with when considering how to build this reading application:

**Single page app or not**  One of the biggest questions I always have when developing an application is wehter to make it a single page app or not. In the case of a book or portions where we know the length of the content we’d be ok using a Single Page Application, but if we’re trying to work with serial content or, in case of a book, we’re trying to provide a serial-like experience we need to load each document in addition to the shell. How to do that without duplicating navigation code in all pages and/or sections. 

We could build the app with content injection and only partial files but I’ve never been fully sold on that idea. What happens when you have javascript disabled or when you’re on LIE-FI (your device thinks it has connection but it’s so poor that it doesn’t work at all) and your device doesn’t connect but it continues to act asif the connection was working. 

I will start with a site built on vanilla HTML, CSS and Javascript with additional libraries that will be described where appropriate. This will allow me to concentrate on the application side rather than worry about the content creation. Others may have different opinions but this is a good place to start. 

## Offline reading

**Is it possible to craft an offline experience for our reading content?**

It would be possible, sure, but it wouldn’t be attractive enough for people who could actually make use of it. 

From my perspective the biggest drawback of any web-based application currently available is that the content would only be available online: the only solution for providing and offline experience left a lot to be desired as chronicled by Jake Archibald in [Application Cache is a Douchebag](http://alistapart.com/article/application-cache-is-a-douchebag) and it was written after a successful App Cache deployment. 

Then Service Workers came into the picture. 

It’s taken me a while to fully warmup to the idea of Service Workers. Yes, they do overcome the drawbacks from App Cache by making everything explicit… If you want the Service Worker to do something you have to explictly code it 

The downside to being forced into explicit behavior is that Service Workers requires coding with new technologies… we can’t rely on implicitb behavior anymore and the way these new APIs are written and coded is new and requires a different mindset than current ES5 code.  If you’re coming from current ES5 environments the learning curve is a little steep as you have to learn Promises and the service worker API. It’s worth it, I assure you. 

Ok, so now we have a way to get the content to display whether the user is online or not. What’s next?

Next we build the shell. 

## Building the shell for the reader. 

The first thing I want to do is build the shell for the application. This also brings up the first set of questions:

* How much of your content do you want to cache on install of the Service Worker so it’ll be ready to go when the user access your app the second time?
* What parts of your reader’s structure will you cache? Why?

First we’ll build the code for the Service Worker. This is a simple Service Worker that will do the following:

* Load and cache the shell for the application 
* Cache any requests for content of our application before displaying it to the user

```javascript
'use strict';
// Chrome's currently missing some useful cache methods,
// this polyfill adds them.
importScripts('serviceworker-cache-polyfill.js');

// Define constants for cache names and versions
const SHELL_CACHE = 'shell_cache';
const SHELL_VERSION = 1;
const CONTENT_CACHE = 'content_cache';
const CONTENT_VERSION = 1;

// Content to  cache when the ServiceWorker is installed
// Change to match the files you need for your app shell.
const SHELL_CONTENT = [
	'/path/to/javascript.js',
	'/path/to/stylesheet.css',
	'/path/to/someimage.png',
	'/path/to/someotherimage.png',
	'/',
	'/offline.html'
];
	
// 1. Register Service Worker
if ('serviceWorker' in navigator) {
	navigator.serviceWorker.register('sw.js');
	// It worked, SW registered
	console.log('ServiceWorker successfully registered');
} else {
	// something happened, SW didn't register
	console.log('ServiceWorker Barfed, did not register: ' + error);
}
	
// 2. Install the Service Worker and cache the shell content.
self.addEventListener('install',  (event) => {
	event.waitUntil(
		caches.open(SHELL_CACHE + ' - v' + SHELL_VERSION)
			.then( (cache) => {
				return cache.addAll(SHELL_CONTENT);
	    })
	    .then(() => {
	    	return self.skipWaiting();
	    })
  );
});
	
// 3. Activate event
self.addEventListener('activate', function(event) {
	// FIXME: Write cache cleaning logic
	return self.clients.claim();
});
	
// 4. Fetch resources
self.addEventListener('fetch', function (event) {
	let request = event.request;
	event.respondWith(
		caches.match(request).then(() => {
			return fetch(request)
			.then((response) => {
				return caches.open(CONTENT_CACHE + '-v' + CONTENT_VERSION)
			.then((cache) => {
				cache.put(event.request, response.clone());
					return response;
				});
			})
		})
	); // closes response
});// closes the fetch event listener
```

With this Service Worker we provide a consistent response regardless of the network (or lack thereof.) Think about it when you add a native application: the load time is very slow the first time but perfromance remains constant in subsequent visits; we no longer need to rely exclusively on the network for our applications. 

We will further enchance the service worker as we move along. We will also explore other ways to create Service Workers programmatically as we move through the design and development process. 

SInce we’ll break down the content of our cache between the shell and the rest of our content it makes sense to cache as little as possible on our shell. This means we will cache the following items:

* HTML index page (including inline CSS to render the [critical path](https://developers.google.com/web/showcase/case-study/service-workers-iowa))
* Javascript (if any) necessary to load the page (other than the Service Worker itself)
* Any images needed for branding the site

In the Service Workder demo above, all the assets need for the shell should be included in the `SHELL_CONTENT` constant. This will be picked up by the script and will add the files to the shell cache. 
  
For future visits the worker will check the cache first and use the content from there, if available and then, only if necessary,  it will go to the network to get the resource we want and store them in the cache to speed things up for the next load. 

Now that we have the  shell we can start playing with the content itself, how we’ll structure it and some additional tricks and extensions. 

## Adding to homesscreen

While it’s been a recent development for Chrome to change the heuristics regarding how to add web applications to the homescreen it only works in mobile and it only works for certain devices. 

The following code goes in the head of an HTML document and it provides basic support across platforms:

```html
<!—- Place favicon.ico in the app/ directory -->
<link rel="icon" type="image/png" href="app/icon.png">

<!-- Chrome for Android theme color -->
<meta name="theme-color" content="#2E3AA1">

<!-- Web Application Manifest -->
<link rel="manifest" href="manifest.json">

<!-- Tile color for Win8 -->
<meta name="msapplication-TileColor" content="#3372DF">

<!-- Add to homescreen for Chrome on Android -->
<meta name="mobile-web-app-capable" content="yes">
<meta name="application-name" content="YOUR NAME HERE">
<link rel="icon" sizes="192x192" href="images/touch/chrome-touch-icon-192x192.png">

<!-- Add to homescreen for Safari on iOS -->
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black">
<meta name="apple-mobile-web-app-title" content="YOUR NAME HERE">
<link rel="apple-touch-icon" href="images/touch/apple-touch-icon.png">

<!-- Tile icon for Win8 (144x144) -->
<meta name="msapplication-TileImage" content="images/touch/ms-touch-icon-144x144-precomposed.png">
```
To activate newer features in Chrome we have to create a  `manifest.json` file and explore what it does.  We’ll shorten the file by removing some of the icon links

```javascript
{
	"name": "Book Reader",
	"short_name": "Book Reader",
	"icons": [{
		"src": "images/touch/icon-72x72.png",
		"sizes": "72x72",
		"type": "image/png"
	}, {
		"src": "images/touch/icon-96x96.png",
	  "sizes": "96x96",
	  "type": "image/png"
	}],
	"background_color": "#3E4EB8",
	"display": "standalone",
	"theme_color": "#2E3AA1"
}
```

We also have icons for multiple resolutions  to acommodate for different resolutions. If you’re not interested in supporting all the resolutions (and I personally wouldn’t) you can skip the resolutions you are not working with. 

Chrome in supported platforms  will use this information and create the application icon. The metadata we added to the head of the document will take care of iOS, Windows 8 and older versions of Chrome and Opera for Android.

## Build system
I’ve given up on the idea of *just using* the web rather than* building* applications for the web. Partly because it’s an exercise in futility and partly because I can now see the advantages of such a system. 

I’ve been working on a [Gulp](http://gulpjs.com/) based system for a while now and documented the intial steps on [my blog](http://publishing-project.rivendellweb.net/?s=gulp%20workflow).  I’ve created a [repository](https://caraya.github.io/gulp-starter/docs/gen/index.html) and [documentation](https://caraya.github.io/gulp-starter/docs/gen/index.html) as a starting point for my work on build systems. Because it is already thoroughly documented I will refer to one task that processes the CSS after the SCSS file is processed. 

Refer to the [gulp-starter Github repository](https://github.com/caraya/gulp-starter) repository for additional information and the gulpfile and package.json files to incorporate the build process into your own projects.

In addition to these tools there are a couple libraries from Google that I want to use in the build process as they make life easier when building Service Worker scripts:  [sw-precache](https://developers.google.com/web/showcase/2015/service-workers-iowa#precaching_with_wzxhzdk14sw-precachewzxhzdk15) and [sw-toolbox / shed](https://developers.google.com/web/showcase/2015/service-workers-iowa#wzxhzdk16sw-toolboxwzxhzdk17_for_all_our_dynamic_needs). 

You use sw-precache with your build system, Gulp in my case, to generate a list of the files to precache.  This is much better than doing it manually… you only have one place to update and the build script will take care of the tedious process.  One way to precache only some files for your project looks like this:

```javascript
// This would most likely be defined elsewhere
const rootDir = myApp;
	
const filesToCache =  [
	rootDir + '/bower_components/**/*.{html,js,css}',
	rootDir + '/elements/**',
	rootDir + '/fonts/**',
	rootDir + '/images/**',
	rootDir + '/scripts/**',
	rootDir + '/styles/**/*.css',
	rootDir + '/manifest.json',
	rootDir + '/humans.txt',
	rootDir + '/favicon.ico',
	rootDir + '/data-worker-scripts.js'
];

// part of the sw-precache library
swPrecache(filesToCache, callback);
```

sw-toolbox does the same for Service Workers. It provides 5 methods that cause requests for URLs matching urlPattern to be resolved by calling handler. Matches requests using the GET, POST, PUT, DELETE or HEAD HTTP methods respectively.

```javascript
toolbox.router.get(urlPattern, handler, options)
toolbox.router.post(urlPattern, handler, options)
toolbox.router.put(urlPattern, handler, options)
toolbox.router.delete(urlPattern, handler, options)
toolbox.router.head(urlPattern, handler, options)
```
If you want more information about these strategies, check Jake Archibald’s [Offline Cookbook](https://jakearchibald.com/2014/offline-cookbook/)

## HTML and grid templates

One of the advantages of working with evergreen browsers is that we can play with the latest CSS technologies and that most of the features that required plugins in the past (audio, video, SVG) are now baked into the platform so we need far fewer plugins to accomplish the same visual display. 

CSS also offers us many more native layout and display options than what we had even a year ago. The two I find the most intriguing are [flexbox](http://publishing-project.rivendellweb.net/new-in-the-css-horizon-css-flexbox/) and [grid layout](http://gridbyexample.com/).   

Flexbox allows you to create repeating content layouts like photo galleries or navigation menus. I’ve been playing with the spec for a while and have created demos like this [image gallery](http://codepen.io/caraya/full/NGoWKM/) to prove the concept. Galleries like this can be included directly on your HTML and don’t really need preprocessors, just a few additional classes on your markup and CSS definitions on your style sheet. 

Grids are newer and more intriguing.  You can create a grid similar to [Skeleton](http://getskeleton.com/#grid), [Bootstrap](http://v4-alpha.getbootstrap.com/layout/grid/) or [Foundation](http://foundation.zurb.com/sites/docs/grid.html) and can be further refined with media queries. The advantage is that we don’t need an additional library and associated overhead for just the grid. 

These are two SASS mixins for a prototype grid system I’m currently working on. 

```scss
@mixin grid-wrapper ($columns: 12, $gutter: 8){
	display: grid;
	margin: 0 auto;
	width: 100%;
	max-width: 960px;
	grid-template-columns: repeat($columns, 1fr); 
	// $columns columns of equal width
	grid-template-rows: auto; 
	// This should make new rows while respecting our column template
	grid-row-gap: ($gutter * 1px);
	grid-column-gap: ($gutter * 1px);
}
```

The first mixin will create the grid itself. In its default configuration it will create a 960px wide grid with 12 columns and 8px gutter. 

```scss
.grid-container {
	@include grid-wrapper()
}
```

The values for the columns and gutters are configurable, if we want a 16 column grid with 16pixel gutters we can do call the mixin like this:

```scss
.grid-container {
	@include grid-wrapper(16, 16)
}
```

And SASS/CSS will create 16 equal columns with 16 pixel gutters between them and 16px gutters between rows. 

The second mixin will place content inside the grid. 

```scss
@mixin placement ($column-start, $column-end, $row-start, $row-end) {
	grid-row: $row-start / $row-end;
	grid-column: $column-start / $column-end;
}
```

We do this by specifying row and column start and end for each element. 

```scss
.figure1 {
	 @include placement(4, 5, 2, 4)
}
```

Using the 16 column grid we created above, we’ll place the `figure ` with class  `figure1` in the corresponding coordinates:

* Starting Colum: 4
* Ending Column: 5
* Starting Row: 2
* Ending Row: 4

** Because the CSS Grid specification is not a recommendation yet it may change from what’s shown here. I’ll continue to test this and update the docs and mixins with appropriate code.**

See Rachel Andrew’s [Grid by example](http://gridbyexample.com/) for ideas and exaples of what you can currently do with CSS Grids. 

As far as HTML is concerned there are several things we need to include in our index.html file before it’ll pass the test of a progressive web application. There are also fallbacks for iOS and older Android devices.  Given all these requirements, the HTML for our index file may look like this:

```html
	<!doctype html>

	<html lang="en">

	<head>
	  <meta charset="utf-8">
	  <meta name="description" content="">
	  <meta name="viewport" content="width=device-width, initial-scale=1">
	  <title>Sample application</title>

	  <!-- Place favicon.ico in the `app/` directory -->

	  <!-- Chrome for Android theme color -->
	  <meta name="theme-color" content="#2E3AA1">

	  <!-- Web Application Manifest -->
	  <link rel="manifest" href="manifest.json">

	  <!-- Tile color for Win8 -->
	  <meta name="msapplication-TileColor" content="#3372DF">

	  <!-- Add to homescreen for Chrome on Android -->
	  <meta name="mobile-web-app-capable" content="yes">
	  <meta name="application-name" content="YOUR NAME HERE">
	  <link rel="icon" sizes="192x192" href="images/touch/chrome-touch-icon-192x192.png">

	  <!-- Add to homescreen for Safari on iOS -->
	  <meta name="apple-mobile-web-app-capable" content="yes">
	  <meta name="apple-mobile-web-app-status-bar-style" content="black">
	  <meta name="apple-mobile-web-app-title" content="YOUR NAME HERE">
	  <link rel="apple-touch-icon" href="images/touch/apple-touch-icon.png">

	  <!-- Tile icon for Win8 (144x144) -->
	  <meta name="msapplication-TileImage" content="images/touch/ms-touch-icon-144x144-precomposed.png">

	  <!-- build:css styles/main.css -->
	  <link rel="stylesheet" href="css/main.css">
	  <!-- endbuild-->
	</head>

	<body>

	  <!-- content goes here -->

	  <!-- build:remove -->
	  <span id="browser-sync-binding"></span>
	  <!-- endbuild -->

	  <h1>Style Guides and Related examples</h1>

	  <!-- Service workers -->
	  <script src="sw-basic.js"></script>
	  <!-- build:js es6/app.js -->
	  <script src="js/app.js"></script>
	  <!-- endbuild-->
	</body>

	</html> 
```

## Javascript plugins and libraries

Transitioning from ES5 (current version of the Javascript language) to ES6 (approved as standard in June, 2015) makes for an interesting choice. 

We now have most, if not all, the construct once only available in libraries like jQuery, MOOTools or Dojo as part of the default language specification. Should we use a library like jQuery or Dojo?  

As much as I woud love to work in plain ES6 there are things that frameworks smooth out a lot of browser bugs and inconsistencies as outlined by  John-David Dalton and Paul Irish in [their answer](https://docs.google.com/document/d/1LPaPA30bLUB_publLIMF0RlhdnPx_ePXm7oW02iiT6o/edit#) to You May Not Need jQuery. 

As suggested by Paul Irish I’ve run the following command against the jQuery source to see how many of these bugs (as defined by the comment Support:) are in the jQuery sourcecode

`curl http://code.jquery.com/jquery-git.js | grep -n Support: | wc -l`

It returned 103 instances where jQuery is working to support some older kind of browser.  

`curl https://code.jquery.com/jquery-3.0.0-beta1.js | grep -n Support: | wc -l`

The same command against the release version of jQuery 3.0 Beta returns 101 instances which is interesting considering that jQuery 3.0 dropped support for IE8. 

I’m inclined to include jQuery 3.0 final but a final decision will depend on what plugins I choose to use, how much would they bloat the source code after processing and how many workarounds would I have to implement to support all the browser versions I want to target. 

Depending on what other libraries we choose to use we may have to run jQuery in no conflict more as [documented in this page](https://learn.jquery.com/using-jquery-core/avoid-conflicts-other-libraries/) to make sure all libraries work as intended. 

Some of the plugins I’m thinking about are listed below.  They may require the page to run the [jQuery Migrate plugin](http://code.jquery.com/jquery-migrate-3.0.0-rc1.js) to check for compatibility issues between the version the plugin was created with and the current version of jQuery (currently 3.0 release.)

Furthermore jQuery 3.0’s modular build process allows you to remove modules from the build, enabling developers to use 3rd party libraries and reducing the size of jQuery itself.

### Modernizr
[Modernizr](https://modernizr.com/) is a feature detection library. It works by doing 2 things: It adds classes for presence or absence of CSS features; it also creates a Modernizr Javascript object that can be used to test for the features we configured for testing. 

To work with CSS we take all the classes Modernizr inserts in the HTML element.  The example below shows all the features from a custom Modernizr build in Chrome 50. 

```
htmlimports cookies geolocation json postmessage serviceworker svg templatestrings typedarrays websockets webaudio supports no-es6array es6collections generators es6math es6number es6object promises no-contains documentfragment audio canvas canvastext contenteditable video webanimations webgl bgpositionshorthand csscalc cssgradients multiplebgs opacity csspointerevents cssremunit rgba csschunit no-es6string mediaqueries unicode fontface generatedcontent lastchild nthchild cssvhunit cssvmaxunit cssvminunit cssvwunit fullscreen indexeddb indexeddb-deletedatabase requestanimationframe raf backgroundblendmode cssanimations bgpositionxy bgrepeatround bgrepeatspace backgroundsize bgsizecover borderradius boxshadow boxsizing csscolumns csscolumns-width csscolumns-span csscolumns-fill csscolumns-gap csscolumns-rule csscolumns-rulecolor csscolumns-rulestyle csscolumns-rulewidth csscolumns-breakbefore csscolumns-breakafter csscolumns-breakinside cssfilters flexbox cssmask shapes csstransforms csstransforms3d csstransitions
```

The same build reflected in Safari 9.1.1 in Yosemite. 

```
no-htmlimports cookies geolocation json postmessage no-serviceworker svg templatestrings typedarrays websockets webaudio supports es6array es6collections no-generators es6math es6number es6object promises no-contains documentfragment audio canvas canvastext contenteditable video no-webanimations webgl bgpositionshorthand csscalc cssgradients multiplebgs opacity csspointerevents cssremunit rgba csschunit no-es6string mediaqueries unicode fontface generatedcontent lastchild nthchild cssvhunit cssvmaxunit cssvminunit cssvwunit fullscreen indexeddb indexeddb-deletedatabase requestanimationframe raf backgroundblendmode cssanimations bgpositionxy bgrepeatround bgrepeatspace backgroundsize bgsizecover borderradius boxshadow boxsizing csscolumns csscolumns-width csscolumns-span csscolumns-fill csscolumns-gap csscolumns-rule csscolumns-rulecolor csscolumns-rulestyle csscolumns-rulewidth csscolumns-breakbefore csscolumns-breakafter csscolumns-breakinside cssfilters flexbox cssmask shapes csstransforms csstransforms3d csstransitions
```
The examples below use the test for HTML audio. 

When working with CSS we create 2 selectors based on the result of the classes Modernizr added.  If the browser does not support audio the class added will be `.no-audio` and we will hide the `#music` element. If we support audio then the class is just `.audio` and we style elements accordingly. 

```css
/* In your CSS: */
	.no-audio #music {
	   display: none; /* Don't show Audio options */
	}
	.audio #music button {
	   /* Style the Play and Pause buttons nicely */
	}
```
When working with Javascript we test on the Modernizr object for the element we want to test, in this case audio. 

```javascript
if (!Modernizr.audio) {
	   /* properties for browsers that do not support audio */
} else{
	   /* properties for browsers that support audio */
}
```
### CSS @supports

An alternative to libraries like Modernizr is to use the `@supports` rule. It takes the full propery that you’re testing for and what to do if the property is supported. 

```css
@supports (display: flex) {
		div { display: flex; }
}
```

You can also use not to negate the test. The example below returns true for browsers that **do not** support the native flex property. 

```css
@supports not (display: flex) {
		div { float: left; } /* alternative styles */
}
```
Many browsers support prefixed versions of  attributes and properties. We can test for them simultaneously using **or** as in the exmple below where we @support will return true if the browser supports any of the flex values. 

```css
@supports (display: -webkit-flex) or
          (display: -moz-flex) or
          (display: flex) {

	    /* use styles here */
}
```
We can also chain our properites to gether and return true only if both properties are supported (and why would you want to use appearance: caret is beyond me, it’s just an example.)

```css
@supports (display: flex) and (-webkit-appearance: caret) {
		/* something crazy here */
}
```

#### JavaScript CSS.supports

The JavaScript counterpart to CSS @supports is window.CSS.supports.  The CSS.supports spec provides two methods of usage.  The first method of usage includes providing two arguments: one for the property and another for the value:
	
`var supportsFlex = CSS.supports("display", "flex");`

The second usage method includes simply providing the entire string to be parsed:

`var supportsFlexAndAppearance = CSS.supports("(display: flex) and (-webkit-appearance: caret)");`

Before using the JavaScript method of supports, it's important to detect the feature first.  Older versions of Opera used a different method name so that throws things for a bit. We need to validate this method is still neccessary
	
`var supportsCSS = !!((window.CSS && window.CSS.supports) || window.supportsCSS || false);`

### Annotation plugin

I’ve always loved the way that Amazon’s Kindle allows you to create a community corpus of annotations of the books you own. I’ve always dreamed for a similar capability of sharing the annotations and marginalia with others but was never able to figure out how.  When I started researching options I found two that I think merit further research

[annotator.js](http://annotatorjs.org/) is a jQuery (1.6 or higher)  based system that allows highlights and annotations directly on top of web content.  I’m guessing that this will also require the JQuery compatibility plugin… which may talk me out of using it altogether.

To use annotator we load the script and css required.  This may change if we concatenate all the scripts together. 

```html
<script src="http://assets.annotateit.org/annotator/v1.2.5/annotator-full.min.js"></script>
<link rel="stylesheet" href="http://assets.annotateit.org/annotator/v1.2.5/annotator.min.css">
```
We then configure the annotator plugin with code like the ones below where we perform multiple configuration tasks for the plugin. 

```javascript
$( document ).ready(function() {
	// Customise the default plugin options with the third argument.
	$('#content').annotator()
	.annotator('setupPlugins', {}, {
		// Disable the tags plugin
		Tags: false,
		// Filter plugin options
		Filter: {
			addAnnotationFilter: false, // Turn off default annotation filter
	    filters: [{label: 'Quote', property: 'quote'}] // Add a quote filter
	  }
	});
});
```

The annotator system also requires [showdown.js](https://github.com/showdownjs/showdown) in order to run Markdown on your annotations, otherwise they are just text.  

My other concern with annotator is the backend requirements.  You have to configure the backend services before you can share your annotations and I have yet to see how well they work offline. 

[Emphasis](https://github.com/NYTimes/Emphasis) is a Library created by the New York Times. It provides a different type of annotation system. It appends a string to the URL that, when pasted into a web browser, wll display the annotations from that URL on the page’s content.  These URLs can be shared between readers to get share the annotations. 

There is currently no way for a user to save their annotations short of copying the URL or bookmarking it.  I’m exploring possibilities of using local storage to save them locally or creating a service that stores them in a database so people can see everyone’s annotations. This would make Emphasis very similar to annotator.js

### Footnotes

I’ve researched libraries for footnotes before. [Bigfoot.js](http://www.bigfootjs.com/) is by far my favorite one mostly because it gives you the option of floating bubbles right next to the footnote mark or the traditional link to the bottom of the page. I also liked the idea of switching between the two types of footnotes. 

I’m also exploring whether Bigfoot can be turned into an endnote script; maybe even pointing to a different page for the footnotes or an annotated bibliography.
 
### Animations

How can we translate work like [Explorable Explanations](http://worrydream.com/ExplorableExplanations/) and [Parable of the Polygonsresearch](http://ncase.me/polygons/) into a book-like experience? is this the right question to ask? it’s part of my quest to better understand animations and their place in the reading experience. 

As usual, there’s more than one way to do it.  Below I’m looking at some options of what you can do. 

#### CSS Animations and Transitions

The most traditional way to animate on the web is to use CSS animations and transitions. They work reliably across browsers and allow a ton of possible work. Because they are geared towards CSS they suffer some of CSS’ drawbacks: they are bound to prefix hell, they may not be supported in all browsers and they may not work with SVG elements, only CSS. 

#### Web Animation API

The [Web Animation API](http://w3c.github.io/web-animations/) is intended to provide the features necessary for expressing CSS Transitions, CSS Animations, and SVG 1.1. As such, the use cases of Web Animations model is the union of use cases for those three specifications.

It’s a modern API and it works across animatable content without having to worry about what type of elements they are: SVG and CSS work equally well with the WAAPI 

#### GSAP

A commercial alternative for animations is GSAP


#### Snap SVG

## Typography, web fonts and lettering

![](http://imgs.xkcd.com/comics/kerning.png)

One of the things I really want to explore with this project is how hard we can push the web for publishing.  This is one of my main reasons why I chose to do Graceful degradation instead of progressive enhancement.  

I want to push the envelope for those browsers that support them and provide experiences that older browsers will be able to handle. Some examples of what I mean are in Lynda.com’s [Advanced CSS Typographic Techniques](https://www.lynda.com/jQuery-tutorials/CSS-Advanced-Typographic-Techniques/116350-2.html) and in Jenn Simmons’ [Layout Labs](http://labs.thewebahead.net/thelayoutsahead/)

 *** Disclaimer: I’m a Lynda.com subscriber but am not getting comission from links to any Lynda.com course ***

## Web Fonts
People tend to forget that web fonts are not really new. Internet Explorer 4 implemented @font-face in 1998 but it did not gain traction for over 10 years… the early implementations didn’t incorporate piracy protections so they were not safe for foundries to work with. 


### Hosting locally versus hosted services

[https://css-tricks.com/snippets/css/using-font-face/](https://css-tricks.com/snippets/css/using-font-face/)
[https://prowebtype.com](https://prowebtype.com/)

### Web Fonts Services

* Typekit
* Google Fonts
* [Font Bureau](http://www.fontbureau.com/)
* [FontShop](https://www.fontshop.com/)
* [Hoefler & Co.](http://www.typography.com/)
* [Webtype](href=%22http://www.webtype.com/)

Hosted locally

* [https://www.fontsquirrel.com/](https://www.fontsquirrel.com/)

## Generated content for Paged Media

There are a lot of things that you can do in printed paged media that you cannot do online, or can you? [CSS Generated Content](https://www.w3.org/TR/css-content-3/) (with some of the useful tools moved to [CSS List and Counters](https://www.w3.org/TR/css-lists-3/)) provides some of that functionality on the web. 

Rather than trying to track how many figures we have in a document we can use CSS like the one below to number them automatically and reset the numbers for each new section of content

	section {
	  counter-increment: section_count;
	  counter-reset: figure_count;
	}
	
	figure {
	  counter-increment: figure_count;
	}
	
	figure figcaption {
	  color: #999;
	  margin-top: -.25em;
	}
	
	figure figcaption::before {
	  content: "Figure " counter(section_count) "-" counter(figure_count) ": ";
	}

This will produce ouput like this:

	Figure 5-3:  Bay Bridge during a grogeous evening


There is a [polyfill](http://philschatz.com/css-polyfills.js/) that covers many of the functions in the specs. 

## Discoverability and metadata
Search engine discoverability may not be enough for publishers in which case we can create additional metadata files like the one from Dave Cramer’s [Epub Zero proposal](https://github.com/dauwhe/epub-zero)

	{
	  "@context": "http://schema.org",
	  "@type": "Book",
	  "accessibilityAPI": "ARIA",
	  "accessibilityControl": [
	    "fullKeyboardControl",
	    "fullMouseControl"
	  ],
	  "accessibilityFeature": [
	    "largePrint/CSSEnabled",
	    "highContrast/CSSEnabled",
	    "resizeText/CSSEnabled"
	  ],
	  "accessibilityHazard": [
	    "noFlashing",
	    "noMotionSimulation",
	    "noSound"
	  ],
	  "aggregateRating": {
	    "@type": "AggregateRating",
	    "reviewCount": "0"
	  },
	  "bookFormat": "EBook/e0",
	  "copyrightHolder": {
	    "@type": "Organization",
	    "name": "Harper & Row"
	  },
	  "author": "Herman Melville",
	  "datePublished": "1851-10-19",
	  "image": "moby-dick-book-cover.jpg",
	  "offers": {
	    "@type": "Offer",
	    "availability": "https://hachettebookgroup.biz/BuyMe?isbn=9780000000000",
	    "price": "6.99",
	    "priceCurrency": "USD"
	  },
	  "copyrightYear": "1851",
	  "description": "Project Gutenberg edition of Moby-Dick",
	  "genre": "Literary Fiction",
	  "inLanguage": "en-US",
	  "isFamilyFriendly": "true",
	  "isbn": "9780000000000",
	  "name": "Moby-Dick",
	  "numberOfPages": "000",
	  "publisher": {
	    "@type": "Organization",
	    "name": "Harper & Row"
	  }
	}
	




