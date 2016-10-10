# Long form publishing with progressive subcompact applications

For the past few months I’ve been working at Google building a set of instructor-led courses on how to build progressive web applications. This has made me think of how to push some of these concepts into what I call “Progressive subcompact publications”.  These concepts are different than ePub Next and any number of formats vining for use, each of which have issues that are hard to overcome:

<ul>
    <li>They seek to replace the installed EPUB (and Kindle) user base.  Since most users of iBooks and Kindles are locked in to their devices and readers this is not a good idea</li>
    <li>There will never be uniform buy in to new specs or ways to publish content and, unless you can get a majority of publishers to implement your specification, schema or idea you will be competing with a behemoth that is very slow to evolve (not questioning the reasons, just making a statement)</li>
    <li>Some people are trying to establish their format as a defacto standard (use this instead of what you already have) and that’s dangerous</li>
    <ul>
        <li>It’s dangerous if you fail to get full buy in because it segments the market even further</li>
        <li>it’s dangerous if you succeed because the defacto standard becomes a dejure standard and you have to support it and work all the warts that were ok when you were developing it (check the Javascript specifications for the amount of baggage carried over to keep old code from breaking)</li>
    </ul>
</ul>

Instead I’m looking at progressive web applications as a starting point for an exploration of how far we can push the web as a publishing medium.

## What are progressive web applications

Alex Rusell  coined the term “Progressive Web Applications” in [Progressive Web Apps: Escaping Tabs Without Losing Our Soul](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/). It is an umbrella term for a series of technologies and best practices to make our users experience feel more like native applications without loosing what makes the web awesome. The characteristics of these apps (as defined in the post) are:

* Responsive: to fit any form factor
* Connectivity independent: Progressively-enhanced with Service Workers to let them work offline
* App-like-interactions: Adopt a Shell + Content application model to create appy navigations & interactions
* Fresh: Transparently always up-to-date thanks to the Service Worker update process
* Safe: Served via TLS (a Service Worker requirement) to prevent snooping
* Discoverable: Are identifiable as “applications” thanks to W3C Manifests and Service Worker registration scope allowing search engines to find them
* Re-engageable: Can access the re-engagement UIs of the OS; e.g. Push Notifications
* Installable in mobile: to the home screen through browser-provided prompts, allowing users to “keep” apps they find most useful without the hassle of an app store
* Linkable: meaning they’re zero-friction, zero-install, and easy to share. The social power of URLs matters.

Note that none of these ideas involve implementing new technologies. They are all in the specification pipeline at W3C or WHATWG and have multiple browser implementations already in the market. 

These technologies also don’t stop you from using the new, shinny and awesome stuff coming down in CSS, Javascript and related APIs and technologies. Nothing stops you from using WebGL 2.0, CSS Grids and other awesomeness coming soon to browsers. 

We will also briefly explore what it would take to make PSPs into full desktop and mobile applications using Electron and Apache Cordoba / Adobe PhoneGap. Again this is not meant to be a perfect solution but an exploration of possibilities. 

## What is subcompact publishing

> It seems that perfection is attained, not when there is nothing more to add, but when there is nothing more to take away.
> Antoine de Saint Exupéry

The term [Subcompact Publishing](http://craigmod.com/journal/subcompact_publishing/) was coined by Craig Mod to describe a new and different publishing methodology rooted in the digital world rather than an extension of traditional publishing methods and systems. 

According to Mod:

*  Subcompact Publishing tools are first and foremost straightforward and require few to no instructions. Compare this to the instructions on how to navigate the current crop of digital magazines

![](http://craigmod.com/images/journal/subcompact/tutorials06.jpg)

![](http://craigmod.com/images/journal/subcompact/tutorials05.jpg)

![](http://craigmod.com/images/journal/subcompact/tutorials03.png)

* The editorial and design decisions around them react to digital as a distribution and consumption space. We no longer buy print magazines but read them online. How can we leverage the online publishing and reading experiences?

* They are the result of dumping our publishing related technology on a table and asking ourselves — what are the core tools we can build with all this stuff? Don’t think of online as just an extension of print but explore what things you can do only online and how that enhances the reader’s experience

Furthermore Craig describes subcompact publications as having the following characteristics:

* **Small issue sizes (3-7 articles / issue)**
*  **Small file sizes** 
* **Digital-aware subscription prices**
* **Fluid publishing schedule** 
* **Scroll (don’t paginate)**
* **Clear navigation**
* **HTML(ish) based** 
* **Touching the open web** 

Reading the essay it shows that it’s geared towards magazines but, with a few modifications, it applies equally to books and other long form content.  For this project, geared towards books and other collection types of publications, I’ve changed some of the definitions of Subcompact Publishing as listed below:

* **Small issue sizes (3-7 articles / issue) / Small file sizes** Because we are using technologies that allow us to load content on demand and to cache the content on the user's browser the need to keep the content small, both issue size and file size becomes less relevant. We can load the shell of our book independently of the content and load the content in smaller bites. For example we can load the first 10 chapters of a book right away and then load the rest of the content on demand. This does not mean we should forget about best practices in compressing and delivering the content but with Service Workers and caching available we can worry more about the content itself rather than how it's delivered. If we add http2 and server push to the mix  the speed gain becomes significant if implemented correctly

* **Fluid publishing schedule** Because we can update the content of our web publications whenever it's necessary we can push new or updated content at any point, without having to worry about releasing the entire package again or having to go through a vendor's store approval process

* **Scroll (don’t paginate)** Unless we have a compelling reason

* **Clear navigation** We have trained our users to accept certain metaphors for navigating our web applications. There is no compelling reason to change that now and, if there is, it better be a very good reason

* **HTML based** Here is the main divergent point from Craig's conception of subcompact publications. PSPs are meant for the web and, if the developer chooses, for HTML-based publishing formats. iBooks and, especially, Kindle are closed ecosystems where it's very difficult to get in to the ecosystem beyond using the tools they provide to adapt your format to their specifications...  It is already hard enough to work with different browsers and the uneven CSS support… none of the existing tools handle epub readers and their own prefixing requirements

* **Using the open web** One of the biggest draws of the web is that it requires no installation proccess or approval for content delivery to the end users. Leveraging this makes the idea of Progressive Subcompact publications easier to work with, even if DRM and other rights management issues are not tackled from the start

This is how a progressive web application looks like. It may also be how our web reading experiences look like in the not too distant future

![Progressive web application - Taken from Google's Using App Install Banners](https://developers.google.com/web/fundamentals/engage-and-retain/app-install-banners/images/add-to-home-screen.gif)

## How does this all work?

At the core of our progressive subcompact publications is a service worker.  This woker is a type of shared worker and it also works as a network proxy for your requests, you can cache responses, provide new responses based on the request you make, provide the basic mechanism to do push notifications and content synchronization in the background. 

We’ll break down the service worker in two sections: the script and the installation script you add to your entry point (usually `index.html`)

### Service worker: The script
This is a fairly common pattern to build a service worker that will perform the following tasks:

* Caches the content of our application shell
* Automatically cleans up old cached content when the service worker is updated
* Fetches app resources using a ‘cache first strategy’. If the content requested is in the cache then serve it from there. If it’s not on the cache then make a network request for the resource, serve it to the user and put it in the cache for later requests

```javascript
var CACHE_NAME = 'my-site-cache-v1';
var cacheWhitelist = [CACHE_NAME];
var urlsToCache = [
	'/',
	'/styles/main.css',
	'/script/main.js',
	'images/banner.png'
];

self.addEventListener('install', function(event) {
	event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});
	
self.addEventListener('activate', function(event) {
  event.waitUntil(
    caches.keys().then(function(cacheNames) {
      return Promise.all(
        cacheNames.map(function(cacheName) {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
	      })
	   	);
	  })
	);
});
	
self.addEventListener('fetch', function(event) {
  event.respondWith(
  caches.match(event.request)
  .then(function(response) {
    if (response) {
      return response;
    }
    
    return fetch(event.request)
      .then(function(response) {
        // Check if we received a valid response
        if (!(response)) {
          throw Error('unable to retrieve file');
        }

      var responseToCache = response.clone();
      caches.open(CACHE_NAME)
        .then(function (cache) {
            cache.put(event.request, responseToCache);
          });
        return response;
      })
      
      .catch(function(error) {
        console.log('[Service Worker] unable to complete the request: ', error);
      });
    })
  );
});
```

There are things that we are not covering on purpose for the sake of keeping the code short. Some of these things include:

* For this example we’ve assumed a minimal set of elements to cache for the application shell. We can be more detailed and add fonts and other static resources. We may also assign the array of items to cache on install to a variable to make it easier to work with
* Providing a solution for when the content is not in the cache and the network is not available. We can cache default feedback for text-based content or programmatically generate an svg image for fallback
* Putting content in different caches so deleting one group of resources doesn’t delete all of them
* We make no effort to add hashes to the resources we cache so we can do proper HTTP cache busting when needed

But at 60 lines of Javascript that will work in 3 of the 5 major browsers (and soon in all of them) I think it does a pretty good job. 

### Service worker: The registration

Assuming that we saved the service worker as `sw.js` we can write the code below inside a script tag on our entry page (`index.html`).

```javascript
if ('serviceWorker' in navigator) {
  console.log('Service Worker is supported');
  navigator.serviceWorker.register('sw.js').then(function(reg) {
    console.log('Yay!', reg);
  }).catch(function(err) {
    console.log('boo!', err);
  });
}
```

This script checks for service worker support by testing if the string `serviceWorker` exists in the navigator object. If it does then service workers are supported, we log a message to the console and then register the serviceworker.

If the serviceWorker string doesn’t exist in the navigator object then service workers are not supported. The catch statement will trigger and we log something to the console.

That’s it. The combination of those two scripts gives us consistent performance across devices and the possibility of work offline after accessing the content once while online. 

## Service Worker: Next step

Doing it by hand is fun and teaches you a lot about the inner workings of service workers but having to update the files you want to cache and how to define the routes you want to use to cache your content. 

[sw-precache](https://github.com/GoogleChrome/sw-precache/blob/master/GettingStarted.md) is a Google tool developed to atuomate creation of service workers with application shell caching on installation. The tool can be used from command line or as part of a build system (Grunt, Gulp and others). 

It will also take care of importing additional scripts to use sw-toolbox (described in the next section). 

A gulpfile.js using sw-precache looks like this:

```javascript
// Assigning modules to local constants
var gulp = require('gulp');
// Required for sw-precache
var path = require('path');
var swPrecache = require('sw-precache');
// Array of paths. Currently only uses the src to represent the path to source
var paths = {
	src: './'
};

gulp.task('service-worker', function(callback) {
  swPrecache.write(path.join(paths.src, 'service-worker.js'), {
  staticFileGlobs: [
    paths.src + 'index.html',
    paths.src + 'js/main.js',
    paths.src + 'css/main.css',
    paths.src + 'images/**/*'
    
    ],
    importScripts: [
      'node_modules/sw-toolbox/sw-toolbox.js',
      paths.src + 'js/toolbox-scripts.js'
    ],
    stripPrefix: paths.src
  }, callback);
});
```

[sw-toolbox](https://googlechrome.github.io/sw-toolbox/docs/master/tutorial-usage) automates dynamic caching for your service worker. It creates customizable routes for your caching and provides for express-like or regular-expression-based routes to match routes and resources. 

In the gulpfile.js abov the `importScripts` section imports two files:

* sw-toolbox.js is the library that will run the custom routes
* toolbox-scripts contains our custom toolbox routing

The script itself is wrapped in an immediately-invoked function expression (IIFE) to keep our code from polluting the global namespace. Inside the IIFE we work with different routes. 

All these routes use the get HTTP verb to represent the action the router will take. 

The toolkbox then takes a pattern to match the route against and a cache strategy. 

There is an optional cache object that contains additional parameters for the cache like (cache) name, maximum number of entries (maxEntries) and maximum duration of the cache in seconds. 

The `toolbox-scripts.js` looks like this:

```javascript
(function(global) {
	'use strict';

	// The route for any requests from the googleapis origin
	global.toolbox.router.get('/(.*)', global.toolbox.cacheFirst, {
		cache: {
			name: 'googleapis',
			maxEntries: 20,
		},
		origin: /\.googleapis\.com$/
	});

// We want no more than 50 images in the cache. 
// We use a cache first strategy
	global.toolbox.router.get(/\.(?:png|gif|jpg)$/, global.toolbox.cacheFirst, {
	  cache: {
	    name: 'images-cache',
	    maxEntries: 50
	  }
	});

	// pull html content using network first
	global.addEventListner('fetch', function(event) {
	  if (event.request.headers.get('accept').includes('text/html')) {
	    event.respondWith(toolbox.networkFirst(event.request));
	  }
	
	  // you can add additional synchronous checks based on event.request.
	});

	// pull video using network only. We don't want such large files in the cache
	global.toolbox.router.get('(.+)', global.toolbox.networkOnly, {
	  origin: /\.(?:youtube|vimeo)\.com$/
	});

	// the default route is global and uses cacheFirst
	global.toolbox.router.get('/*', global.toolbox.cacheFirst);
})(self);
```

Registering the automatically generated service worker is no different than registering the manually generated script. Assuming that we saved the service worker as `service-worker.js`the registration code in our entry page (`index.html`) looks like this:

```javascript
if ('serviceWorker' in navigator) {
 console.log('Service Worker is supported');
 navigator.serviceWorker.register('sw.js').then(function(reg) {
   console.log('Yay!', reg);
 }).catch(function(err) {
   console.log('boo!', err);
 });
}
```

## What can we do beyond offline?

The service worker script we discussed in the prior section is the core of a PSP but there’s way more we can do to make our reading experiences behave more like native applications.  Some of these features are 

* Push notifications
* Background sync

While not directly related to service workers this feature may help get better re-engagement from your users:

* Installation on mobile homescreens

Also not directly related to progressive web applications, we can also preserve data, not just content on our web applications using

* IndexedDB

We’ll discuss them in the sections below.

### Push notifications

Using Push notifications we can communicate events and new information to the user through the Operating System’s push notification system and UI. 

![](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/images/cc-good.png)

Detailed instructions for setting up Push Notifications using Chrome and Firebase Cloud Messaging (the successor to Google Cloud Messaging) can be found in [Push Notifications on the Open Web](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web?hl=en). 

[Push Notifications: Timely, Relevant, and Precise](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/) provide a context for how and when to use push notifications. 

### Background Sync

If you write an email, instant message, or simply favourite a tweet, the application needs to communicate that data to the server. If that fails, either due to user connectivity, service availability or anything in-between, the app can store that action in some kind of 'outbox' for retry later.

Unfortunately, on the web, that outbox can only be processed while the site is displayed in a browsing context. This is particularly problematic on mobile, where browsing contexts are frequently shut down to free memory.

This API provides a web equivalent to native application platforms’ [job scheduling APIs](https://developer.android.com/reference/android/app/job/JobScheduler.html) that enable developers to collaborate with the system to ensure low power usage and background-driven processing. The web platform needs capabilities like this too. In the future we'll be able to do periodic synchronizations. 

A more detailed explanation can be found in the [explainer document for background sync](https://github.com/WICG/BackgroundSync/blob/master/explainer.md).

### Install in mobile home screens

Using the [W3C App Manifest specification](https://www.w3.org/TR/appmanifest/) and the existing metatags for adding an app to the homescreen in mobile devices we enable our users to add our web content to the homescreen of mobile devices to foster a higher level of interaction and reengagement with the content.  

[HTML5 Doctor](http://html5doctor.com/web-manifest-specification/) has a good an up to date reference on App Manifest. Another source of information is the Mozilla Developer Network article on [Web App Manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest). 

### IndexedDB

We've had client-side storage solutions for a while now. Sessions Storage, WebSQL and IndexedDB. Until recently they had no uniform support among brosers and one (WebSQL) is no longer being developed because all the implementations relied on [SQLite](https://sqlite.org/) as the backend and this was considered to violate the "two interoperable implementations" requirements for W3C specs. 

I've chosen IndexedDB as the engine to store data for my offline applications because, as complicated as the API is work with, there are wrapper libraries that make the work easier and will work across browsers, even Safari (which has a deserved reputation for shitty IndexDB implementations). 

Knowing how much of a pain it can be to write bare IndexedDB code, I've picked [Dexie](http://dexie.org/) as my wrapper library. It is easy to use and, for browsers who have issues with indexedDB like Safari, provide a transparent fallback to WebSQL.  It also uses promises rather than callbacks and, once you start working with promises, you will never go back to callbacks :-)

The example below shows how to create a database and a store for the a theoretical friends datastore.
 
```javascript
var db = new Dexie("friends");

// Define a schema
db.version(1).stores({
  friends: 'name, age'
});
```

We then open the database

```javascript
// Open the database
db.open().catch(function(error) {
  alert('Uh oh : ', error);
});
```

We can then insert records into the datastore one at a time or using a transaction. 

Transactions group one or more actions into an atomic unit. If any of the actions composing a transaction fails then the entire transaction fails and the datasore is rolled back to the state before the transaction began.

```javascript
// Insert data into the database
db.friends.add({
  name: 'Camilla', age: 25
});

// Insert data into database using transactions
function populateSomeData() {
  return db.transaction("rw", db.friends, function () {
    db.friends.clear();
    db.friends.add({ name: "David", age: 48 });
    db.friends.add({ name: "Ylva", age: 21 });
    db.friends.add({ name: "Jon", age: 76 });
    db.friends.add({ name: "Måns", age: 56 });
    db.friends.add({ name: "Daniel", age: 55 });
    db.friends.add({ name: "Nils", age: 42 });
    db.friends.add({ name: "Zlatan", age: 21 });
        
    // Log data from DB:
    db.friends.orderBy('name').each(function (friend) {
        log(JSON.stringify(friend));
    });
  })
  .catch(function (e) {
    log(e, "error");
  });
}
 ```
We can then retrieve data from the store usning queries similar to the SQL syntax. An example of this query retrieves all the names from the data store where the age is over (above) 35 and then display the names.  

```javascript
// Query friends datastore
db.friends
  .where('age')
  .above(35)
  .each (function (friend) {
    console.log (friend.name);
  });
```

There may be occasions when we need to delete the database, maybe because we don't need it again or maybe because we screwed up and want to start over.  

```javascript
db.delete().then(function() {
    console.log("Database successfully deleted");
}).catch(function (err) {
    console.error("Could not delete database");
}).finally(function() {
    // Do what should be done next...
});
```

This is a very broad and quick overview of Dexie. If you want more information check [the Dexie.js Tutorial](https://github.com/dfahlander/Dexie.js/wiki/Tutorial) to get started. 

There are other wrapper libraries for IndexedDB

## Other fun things we can do

### Annotations
I still remember the first time I made an annotation from a Kindle book available in  their public site ([kindle.amazon.com](http://kindle.amazon.com/)). I saw the possibilities as limitless until I realized that there were limitless as long as you bought the book from Amazon and read it on a Kindle device or application. 

Every time I’ve turned around and searched for some way to annotate the web I've come with these two solutions but I've never had a project they work well with. I think PSPs are the perfect place to put this in practice. There are two libraries I think are particularly appropriate: [Emphasis](#) and [annotator.js](http://annotatorjs.org/) which provide different ways to make and share annotations from your PSPs.

Emphasis provides dynamic paragraph-specific anchor links and the ability to highlight text in a document, it makes the information about the highlighted elements of the page available in the URL hash so it can be emailed, bookmarked, or shared.

annotator provides a more traditional annotation interface that is closer in spirit to the Kindle annotation UI that attracted me to the concept when I first saw it. 

![](http://annotatorjs.org/images/thumb-add.png) ![](http://annotatorjs.org/images/thumb-viewer.png)![](http://annotatorjs.org/images/thumb-bookmarklet.png)

Another tool that sounds interesting is MIT’s [Annotation Studio](http://www.annotationstudio.org/) but it seems to be geared towards MIT Hyperstudio’s larger project and not necessarily ready as a standlone solution, that said, your milleage may vary. 

The thing to consider if how these annotation tools store the annotations. Do they use server-side databases? If so how do we cache new annotations when the reader is offline? Google Analytics provides a possible example where we store the annotations in indexedDB and then play them back when the user goes online. 

## Structuring and styling our content
We want our content to look awesome regardless of the device. How do we accomplish this?

A good starting point is Ethan Marcotte’s [Responsive Web Design](https://abookapart.com/products/responsive-web-design) (or whatever Responsive Web Design book is your favorite). We want the experience to scale to whatever device or platform we’re targeting and, whatever design we choose, the first thing we need to make sure of is that it’ll work well in phones, tablets and desktops (in other words, everywhere there is a browser). 

Once we have a layout we can start thinking about, what to me is, the most important part of any long form project: typography.  I’ve [written extensively](https://caraya.github.io/book-app/app/typography.html) on what typography is and how it works on the web now we need to take the next steps. 

This is where the first set of choices happen: What layout do we choose? How do we create something engaging without becoming repetitive? How do we craft a reading experience that matches the content?

<div class="video">
<iframe width="560" height="315" src="https://www.youtube.com/embed/kRYrbcGWjzU?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

I first saw Jen’s presentation at SFHTML5. I see it as a challenge and an opportunity to think differently about the way we create and layout our content on the web.  For longer form content this also speaks to letting the content dictate the layout and not the other way around.  What is it that makes magazine layouts so interesting? 

I collect electronic versions of GQ, Wired, Vanity Fair, Fast Company and Harvard Business Review and the biggest question when I read them is how can we make this reading experience in the open web? The ads from magazines are what intrigue me the most… and where a lot of my most radical ideas come from. 

<div class="video">
<iframe src="https://player.vimeo.com/video/112865159?color=9c191e" width="560" height="315" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
</div>

After watching this presentation from Beyond Tellerand I couldn’t help reading the new edition of [Hardboiled Web Design](https://shop.smashingmagazine.com/products/hardboiled-web-design). Clarke advocates that creativity should be at the center of our online design work... It speaks to the need of art directed web design and bespoke designs rather than using the same design over and over. 

If we drop the book metahphor from our online reading experiences, There is no limit to what we can do with our online publications.   We need to go back to our content and see how we can enrich it and what technologies we can use to do so… we have a lot of layout tools that, a few years ago, were only possible in InDesign and other Desktop Publishing Tools or took a lot of extra workarounds to do in CSS/HTML/JavaScript. 

Now we need to get out our collective comfort zone and challenge both ourselves and our future readers with layouts that go beyond what we see on the web today.  

One last example of what we can do with our new css tools and how much we can be true to our creative selves without having to lie to our web developer selves: Justin McDowell uses new CSS technologies to recreate works from the Bauhaus school. 

<div class='video'>
<iframe width="560" height="315" src="https://www.youtube.com/embed/I49NX1C8qt8" frameborder="0" allowfullscreen></iframe>
</div>

## How have reader expectations changed? (UI for the performance obsessed)
as consumers of information we’ve all become more demanding. We want the content faster and we want to be engaged with the content we access online.  PSPs should be no different in their performance to traditional web applications. 

Rather than provide a one-size-fits-all solution I present some of the data Nielsen first articulated in 1993’s *Usability Engineering * We’ll use these values to draw some basic conclusions that will start the thinking about performance and how PSPs can work towards achieving those performance goals.

* **0.1 seconds** — Operations that are completed in 100ms or fewer will feel instantaneous to the user. This is the gold standard that you should aim for when optimising your websites.
* **1 second** — Operations that take 1 second to finish are generally OK, but the user will feel the pause. If all of your operations take 1 second to complete, your website may feel a little sluggish.
*  **10 seconds** — If an operation takes 10 seconds or more to complete, you’ll struggle to maintain the user’s attention. They may switch over to a new tab, or give up on your website completely. Of course this depends on what operation is being completed. For example, users are more likely to stick around if they’ve just submitted their card details in the checkout than if they’re waiting to load a product page.
*  **16 milliseconds** — Given a screen that is updating 60 times per second, this window represents the time to get a single frame to the screen (1000 ÷ 60 = ~16). People are exceptionally good at tracking motion, and they dislike it when their expectation of motion isn’t met, either through variable frame rates or periodic halting.

We want to get to the site’s first meaningful paint on initial load in as close to 1000 milliseconds as possible. 

Animations should take no more than 16 milliseconds in order to reach 60 frames a second.

## Performance Optimizations
We can optimize our resources so that time to first meaningful interaction is as short as possible. First load will also cache the resources needed for our application shell and then dynamically .  We want to optimize this to last as little as possible.  

This is not just speech for the sake of speach. Take the following graphic (from Soasta’s [Page bloat update: The average web page is more than 2 MB in size](https://www.soasta.com/blog/page-bloat-average-web-page-2-mb/)).  How can we minimize the number of resources to load and cache the first time we access a page? How many of these resources can be reused on pages accross the site? How can we optimize images to reduce their size?

![](https://www.soasta.com/wp-content/uploads/2015/06/page-bloat-images.png)

We have gotten lazy or, possibly, made the wrong assumptions. The graphic below, also from Soasta’s blog post, show how the size of our web content has changed over the years… and it shows no signs of decreasing. 

![](https://www.soasta.com/wp-content/uploads/2015/06/page-bloat-May15-page-composition.png)

For a more complete perspective checkout Udacity’s [Website Performance Optimization](https://www.udacity.com/course/website-performance-optimization--ud884) course. 

## How are you serving your content?

PSPs do not avoid performance requirements... however the questions are slightly different. Are you serving your content with HTTP 1.x or HTTP2? When I first heard the question asked I laughed… why should this matter?

HTTP2 has changed the way we work with web content. What we used to consider as patterns and best practices are no longer necessay and may be considered anti patterns.  

Because of the way HTTP2 serves the content it is not necessary to concatenate our CSS and Javascript, it actually work better if you work with individual minimized files; HTTP2:

* is binary, instead of textual: Binary protocols are more efficient to parse, more compact “on the wire”, and most importantly, they are much less error-prone, compared to textual protocols like HTTP/1.x, because they often have a number of affordances to “help” with things like whitespace handling, capitalization, line endings, blank lines and so on
* is fully multiplexed: Multiplexing addresses [head-of-line-blocking](https://www.wikiwand.com/en/Head-of-line_blocking) by allowing multiple request and response messages to be in flight at the same time; it’s even possible to intermingle parts of one message with another on the wire
* can use one connection for parallelism: One application opening many connections simultaneously breaks a lot of the assumptions that TCP was built upon; since each connection will start a flood of data in the response, there’s a real risk that buffers in the intervening network will overflow, causing a congestion event and retransmits
* uses header compression to reduce overhead:  If you assume that a page has about 80 assets and each request has 1400 bytes of headers, it takes at least 7-8 round trips to get the headers out “on the wire”. In comparison, even mild compression on headers allows those requests to get onto the wire within one round trip – perhaps even one packet.
* allows servers to “push” responses proactively into client caches: When the server pushes the content that the client will need before it needs it we can save networks requests by caching the content in the browser (HTTP) cache 

This doesn’t mean we shouldn’t minimize and compress resources but bundling them together is less important now that we don’t have to be concerned with saturating the connection to the server and can leverage HTTP2 push to let the server prefetch content to the client.  

Improving performance is a never ending game. Ilya Grigorik illustrates this point in his presentation from I/O 2016. 

<div class="video">
<iframe width="560" height="315" src="https://www.youtube.com/embed/aqvz5Oqs238?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

## A basic PSP Checklist



## Links, resources, patterns and ideas
* Responsive Web Design
	* [Responsive Web Design](https://abookapart.com/products/responsive-web-design) — Ethan Marcotte, A Book Apart
	* [Responsible Responsive Design](https://abookapart.com/products/responsible-responsive-design) — Scott Jehl, A Book Apart
	* [Going Responsive](https://abookapart.com/products/going-responsive) — Karen McGrane, A Book Apart
	* [Responsive Design: Patterns and Principles](https://abookapart.com/products/responsive-design-patterns-principles) — Ethan Marcotte, A Book Apart
	* [Design For Real Life](https://abookapart.com/products/design-for-real-life) — Eric Mayer & Sara Wachter-Boettcher, A Book Apart
	* [Inclusive Design Patterns](https://www.smashingmagazine.com/books/#inclusive-design-patterns) — Haydon Pickering, Smashing Books
* Service Workers
	* [caniuse.com support matrix](http://caniuse.com/#feat=serviceworkers)
	* [Specification](https://w3c.github.io/ServiceWorker/)
	* [Is ServiceWorker ready?](https://jakearchibald.github.io/isserviceworkerready/resources.html) — Jake Archibald
	* [Service Workers: an Introduction](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers) — Web Fundamentals
	* [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) — MDN 
	* Support by browsers
		* [MS Edge](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/serviceworker/)
		* [WebKit - Safari](https://webkit.org/status/#specification-service-workers)
		* [Firefox](https://platform-status.mozilla.org/#service-worker)
		* [Chrome / Opera](https://www.chromestatus.com/feature/6561526227927040)
	* Background Sync
		* [Explainer](Background%20synchronization%20explained)
* Accessibility
	* [A11y Project](http://a11yproject.com/)
	* Chrome [Accessibility Developer Tools](accessibility-developer-tools)
	* [A11y accessibility checker](https://www.w3.org/TR/appmanifest/)
	* [Introduction to web accessibility course](https://webaccessibility.withgoogle.com/course) — Google
	* [The WAI Forward](https://www.smashingmagazine.com/2014/07/the-wai-forward/) — Heydon Pickering, Smashing Magazine
	* [Colour Contrast Analyser](https://www.paciellogroup.com/resources/contrastanalyser/)— The Paciello Group
	* [Accessibility Testing Tools Updated](https://www.paciellogroup.com/blog/2014/10/accessibility-testing-tools-updated/) — The Paciello Group
* Service Worker related
	* Web Push Notifications
		* [Web Push Notifications: Timely, Relevant, and Precise](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/) — Web Fundamentals
		* [What Makes a Good Notification?](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/good-notification)
		* [Video: Web Push Notifications](https://www.youtube.com/watch?v=_dXBibRO0SM&ab_channel=GoogleChromeDevelopers) — Google I/O 2016
		* [Bringing Push Notifications to the Mobile Web](https://www.youtube.com/watch?v=HbmcnjWFGbY&ab_channel=@Scale) — @scale
		* [Deep Engagment with Push Notifications](https://www.youtube.com/watch?v=Zq-tRtBN3ws&ab_channel=GoogleChromeDevelopers) — Progressive Web App Summit 2016
	* Installation on mobile home screen
		* [Add to homescreen](https://developer.chrome.com/multidevice/android/installtohomescreen) — Android
		* [Web App Install Banners](https://developers.google.com/web/fundamentals/engage-and-retain/app-install-banners/?hl=en) — Chrome for Android
		* [Increasing Engagement with Web App Install Banners](https://developers.google.com/web/updates/2015/03/increasing-engagement-with-app-install-banners-in-chrome-for-android) — Chrome for Android
		* [Configuring Web Applications](https://developer.apple.com/library/content/documentation/AppleApplications/Reference/SafariWebContent/ConfiguringWebApplications/ConfiguringWebApplications.html) — iOS
		* [Web manifest specification](http://html5doctor.com/web-manifest-specification/) — HTML5 Doctor
* Peformance
	* General Readings
		* [High Performance Browser Networking](https://hpbn.co/) — Ilya Grigorik, O’Reilly
		* [Web Performance Warrior: The business of speed](http://www.oreilly.com/webops-perf/free/web-performance-warrior.csp) - Andy Still, O’Reilly
	*  Performance Patterns
		* [Introducing RAIL: A User-Centric Model For Performance](https://www.smashingmagazine.com/2015/10/rail-user-centric-model-performance/)
		* [Serve your content](https://www.polymer-project.org/1.0/toolbox/server) (introduces the PRPL pattern)
	* HTTP2
		* [High Performance Browser Networking, chapter 12](https://hpbn.co/http2/) — Ilya Grigorik, O’Reilly
		* [Are you ready for HTTP/2 Server Push?!](https://blogs.akamai.com/2016/04/are-you-ready-for-http2-server-push.html)  — Akhil Jayaprakash
		* [Innovating with HTTP2 server push](https://www.igvita.com/2013/06/12/innovating-with-http-2.0-server-push/) — Ilya Grigorik
	* Readings
		* [Performance: Showing Versus Telling](http://alistapart.com/article/performance-showing-versus-telling) — Lara Hogan
		* [An Introduction to perceived performance](http://blog.teamtreehouse.com/perceived-performance) — Matt West
		* [Response Times: The 3 Important Limits](https://www.nngroup.com/articles/response-times-3-important-limits/) — Jakob Nielsen
		* [Powers of 10: Time Scales in User Experience](https://www.nngroup.com/articles/powers-of-10-time-scales-in-ux/) — Jakob Nielsen
		* [Response time in man-computer conversational transactions](http://theixdlibrary.com/pdf/Miller1968.pdf) — Robert Miller
		* [The information visualizer: An information workspace](http://www2.parc.com/istl/groups/uir/publications/items/UIR-1991-01-Card-CHI91-IV.pdf) — Card, Robertson and Mackinlay
		* [A Beginner's Guide to Perceived Performance: 4 Ways to Make Your Mobile Site Feel Like a Native App](http://dev.mobify.com/blog/beginners-guide-to-perceived-performance/) — Kyle Peatt
		* [A Study on Tolerable Waiting Time: How long Are Web Users Willing to Wait?](http://sighci.org/uploads/published_papers/bit04/BIT_Nah.pdf)— Fiona Fui-Hoon Nah
		* [Interaction in 4-Second Bursts: The Fragmented Nature of Attentional Resources in Mobile HCI](http://www.interruptions.net/literature/Oulasvirta-CHI05-p919-oulasvirta.pdf)— Antti Oulasvirta, Sakari Tamminen, Virpi Roto, and Jaana Kuorelaht
		* [Quantifying Interactive User Experience on Thin Clients](http://isr.cmu.edu/doc/tolia06-ieee.pdf)— Niraj Tolia, David G. Andersen, and M. Satyanarayanan
		* [Characterizing Web Use on Smartphones](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.366.1170&amp;rep=rep1&amp;type=pdf)— Chad C. Tossell, Philip Kortum, Ahmad Rahmati, Clayton Shepard, Lin Zhong
		* [Playing With Tactile Feedback Latency in Touchscreen Interaction: Two Approaches](http://link.springer.com/chapter/10.1007%2F978-3-642-23771-3_42)— Topi Kaaresoja, Eve Hoggan, Emilia Anttila

