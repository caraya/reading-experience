# Long form publishing with  progressive subcompact applications

For the past few months I’ve been working on a set of instructor-led courses on how to build progressive web applications. This has made me think of how to push some of these concepts into what I call “Progressive subcompact publications”.  These concepts are different than ePub Next and [5doc](http://www.5doc.org/) each of wich have issues that are hard to overcome:

* They seek to replace the installed EPUB (and Kindle) user base.  Since most users of iBooks and Kindles are locked in to their devices and readers this is not a good idea
* There will never be uniform buy in to new specs or ways to publish content and, unless you can get a majority of publishers to implement your specification, schema or idea you will be competing with a behemoth that is very slow to evolve (not questioning the reasons, just making a statement)
* 5Doc is trying to establish itself as a defacto standard (use this instead of what you already have) and that’s dangerous. 
	* It’s dangerous if you fail to get full buy in because it segments the market even further
	* it’s dangerous if you succeed because the defacto standard becomes a dejure standard and you have to support it and work all the warts that were ok when you were developing it (check the Javascript specifications for the amount of baggage carried over to keep old code from breaking)

Instead I’m looking at the progressive web applications as a starting point for an exploration of how far we can push the web as a publishing medium.

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

* **Small issue sizes (3-7 articles / issue) / Small file sizes** Because we are using technologies that allow us to load content on demand and to cache the content on the user's browser the need to keep the content small, both issue size and file size becomes less relevant. We can load the shell of our book independently of the content and load the content in smaller bites. For example we can load the first 10 chapters of a book right away and then load the rest of the content on demand  
	  
	This does not mean we should forget about best practices in compressing and delivering the content but with Service Workers and caching available we can worry more about the content itself rather than how it's delivered. If we add http2 and server push to the mix  the speed gain becomes significant if implemented correctly

* **Fluid publishing schedule** Because we can update the content of our web publications whenever it's necessary we can push new or updated content at any point, without having to worry about releasing the entire package again or having to go through a vendor's store approval process

* **Scroll (don’t paginate)** Unless we have a compelling reason

* **Clear navigation** We have trained our users to accept certain metaphors for navigating our web applications. There is no compelling reason to change that now and, if there is, it better be a very good reason

* **HTML based** Here is the main divergent point from Craig's conception of subcompact publications. PSPs are meant for the web and, if the developer chooses, for HTML-based publishing formats. EPUB and, especially, Kindle are closed ecosystems where it's very difficult to get in to the ecosystem beyond using the tools they provide to adapt your format to their specifications...  It is already hard enough to work with different browsers and the uneven CSS support… none of the existing tools handle epub readers and their own prefixing requirements

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

	var CACHE_NAME = 'my-site-cache-v1';
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
	  var cacheWhitelist = [CACHE_NAME];
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
	            console.log('I am unable to complete the request: ', error);
	          });
	      })
	  );
	});

There are things that we are not covering on purpose for the sake of keeping the code short. Some of these things include:

* For this example we’ve assumed a minimal set of elements to cache for the application shell. We can be more detailed and add fonts and other satic resources
* Providing a solution for when the content is not in the cache and the network is not available. We can cache default feedback for text-based content or programmatically generate an svg image for fallback
* Putting content in different caches so deleting one group of resources doesn’t delete all of them
* We make no effort to add hashes to the resources we cache so we can do proper HTTP cache busting when needed

But at 60 lines of Javascript that will work in 3 of the 5 major browsers (and soon in all of them) I think it does a pretty good job. 

### Service worker: The registration

Assuming that we saved the service worker as `sw.js` we can write the code below inside a script tag on our entry page (`index.html`).

	if ('serviceWorker' in navigator) {
	 console.log('Service Worker is supported');
	 navigator.serviceWorker.register('sw.js').then(reg => {
	   console.log('Yay!', reg);
	 }).catch(err => {
	   console.log('boo!', err);
	 });
	}

This script checks for service worker support by testing if the string `serviceWorker` exists in the navigator object. If it does then service workers are supported, we log a message to the console and then register the serviceworker.

If the serviceWorker string doesn’t exist in the navigator object then service workers are not supported. The catch statement will trigger and we log something to the console.

That’s it. The combination of those two scripts gives us consistent performance across devices and the possibility of work offline after accessing the content once while online. 

## What can we do beyond offline?

The service worker script we discussed in the prior section is the core of a PSP but there’s way more we can do to make our reading experiences behave more like native applications.  Some of these features are 

* Push notifications
* Background sync

While not directly related to service workers this feature may help get better re-engagement from your users:

* Installation on mobile homescreens

We’ll discuss them briefly in the sections below.

### Push notifications

Using Push notifications we can communicate events and new information to the user through the Operating System’s push notification system and UI. 

![](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/images/joe-asked-contextual.png)

Detailed instructions for setting up Push Notifications using Chrome and Firebase Cloud Messaging (the successor to Google Cloud Messaging) can be found in [Push Notifications on the Open Web](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web?hl=en). 

[Push Notifications: Timely, Relevant, and Precise](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/) provide a context for how and when to use push notifications. 

### Background Sync

If you write an email, instant message, or simply favourite a tweet, the application needs to communicate that data to the server. If that fails, either due to user connectivity, service availability or anything in-between, the app can store that action in some kind of 'outbox' for retry later.

Unfortunately, on the web, that outbox can only be processed while the site is displayed in a browsing context. This is particularly problematic on mobile, where browsing contexts are frequently shut down to free memory.

This API provides a web equivalent to native application platforms’ [job scheduling APIs](https://developer.android.com/reference/android/app/job/JobScheduler.html) that enable developers to collaborate with the system to ensure low power usage and background-driven processing. The web platform needs capabilities like this too.

A more detailed explanation can be found in the [explainer document for background sync](https://github.com/WICG/BackgroundSync/blob/master/explainer.md).

### Install in mobile homescreens
Using the [W3C App Manifest specification](https://www.w3.org/TR/appmanifest/) and the existing metatags for adding an app to the homescreen in mobile devices we enable our users to add our web content to the homescreen of mobile devices to foster a higher level of interaction and reengagement with the content.  

[HTML5 Doctor](#) has a good an up to date reference on App Manifest. Another source of information is the Mozilla Developer Network article on [Web App Manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest). 

## Structuring and styling our content
We want our content to look awesome regardless of the device. How do we accomplish this?

I like the way Polymer does their default appication when running the Polymer CLI.  It shouldn’t be too hard to duplicate that layout using Foundation or Material Design Light. 

Once we have a layout we can start thinking about, what to me is, the most important part of any long form project: typography.  I’ve [written extensively](https://caraya.github.io/book-app/app/typography.html) on what typography is and how it works on the web now we need to take the next steps. 

<div class="video">
<iframe width="560" height="315" src="https://www.youtube.com/embed/kRYrbcGWjzU?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

I first saw Jen’s presentation in SFHTML5. I see it as a challenge and an opportunity to think differently about the way we create and layout our content on the web. 

<div class="video">
<iframe src="https://player.vimeo.com/video/112865159?color=9c191e" width="560" height="315" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
<div>

After watching this presentation from Beyond Tellerand I couldn’t help reading the new edition of [Hardboiled Web Design](https://shop.smashingmagazine.com/products/hardboiled-web-design). Clarke advocates that creativity should be at the center of our online design work... It speaks to the need of art directed web design and bespoke designs rather than using the same design over and over. 

If we drop the book metahphor from our online reading experiences, There is no limit to what we can do wit our online publications.   We need to go back to our content and see how we can enrich it and what technologies we can use to do so. 

A good starting point is Ethan Marcotte’s [Responsive Web Design](https://abookapart.com/products/responsive-web-design) (or whatever Responsive Web Design book is your favorite)

## How have reader expectations changed?
as consumers of information we’ve all become more demanding. We want the content faster and we want to be engaged with the content we access online.  PSPs should be no different in their performance to traditional web applications.

(Add stats about attention span)

## Do all paradigms for copy protection and owner rights apply?

## Is it useful to build cross platform versions of our publications? If so what tool should we use?

### Electron

### Cordova / Phone Gap

## Links, Resources and Recipes 


