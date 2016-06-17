#  Progressive and Subcompact Books: Philosophy

> ***This is meant as living document. Feedback is appreciated and will be incorporated when appropriate. The idea is to use this and its sister technical  document as the basis for a proof of concept application***

One of the first web reading experiences I saw was Craig Mod’s [Art Space Tokyo](#). I use the term experience deliberately as it was one of the first efforst to create a consistent experience (or as much as possible)  between the physical book and the different online versions available to the user. They all share the same illustrations the same basic structure and layout.  They also play to the strengths of each platform. 

I’ve worked in creating digital content for the web as well as ebooks in epub and Kindle formats and it’s a mess. Most, if not all readers have their incompatible idiosyncracies that make creating CSS that works reliably accross devices. That plus the  sheer number of form factors for ebook readers (each introducing their own screen size, pixel density, CSS parser, standard version they support and other considerations) makes it evern harder to work reliably with ebooks. 

I’ve been around the web long enough to realize that we should be able to publish our content online on the open web as easily as we do for our ereading devices.  I’ve explored different ways to accomplish this on my research and projects. I’ve looked at building easy frameworks to add you HTML to, I’ve built custom frameworks for publishing XML content as HTML and PDF, have worked with Docbook and DITA, and have explored what Web Components can offer as a publishing platform among other ideas. 

But it wasn’t until I saw Progressive Web applications that web-based reading applications that are on par with native experiences became possible.  These progressive ebooks are not only readable across form factors but they are also available offline and can, if so configured, send push notifications on new content updates and synchronize the content on the background while the reader is busy doing something else… the service worker attached to the book will handle most of these experience issues without the user’s direct input and without necessarily having to be in the page or with the browser open. 
 
The term Progressive Web Application was first coined by Alex Russell  in [Progressive Web Apps: Escaping Tabs Without Losing Our Soul](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/). In the article Alex describes the requirements for a web application that becomes a close match to a native app using existing and new web technologies.  Russell sees the following as core characteristics of progressive web applications:

* Use Responsive Web Design to provide a consistent user experience
* Progressively-enhanced with Service Workers to let them work offline
* Adopt an application shell model to create an app-like user experience
* Use background synchronization to keep the content up to date
* Served via HTTPS to prevent snooping
* Identified as “applications” allowing search engines to find them
* Can use the notification system built into the operating system to push notifications about the application
* Installable to the home screen through browser-provided prompts. Users can “save” apps to their home screens without intermediaries

I became interested on the the idea of combining Progressive Web Apps with [Subcompact Publishing](#) when I started looking at using offline as a starting point to experiment with web content and how can we make web content as engaging as applications or devices like Kindle, Kobo and hundreds of others readers.  App Cache, the current solution at the time, proved to be hard to implement and unreliable in its execution but it was all we had.

What would a web based reading application look like if, in addition to the requirements for a progressive web application, we could also make it small and nimble enough to meet the criteria for a subcompact publication (modified from Craig’s original):

* Small file sizes
* Fluid publishing schedule
* Scroll (don’t paginate)
* Clear navigation
* HTML based
* Touching the open web

The progressive part of our reader gives us both a content shell to hold our content and, at the same time,  provides a stable platform that make flaky connections less annoying. this is just a starting point. 

Subcompact publishing makes the content easier to create and digest. Progressive Web Apps makes it easier to push these smaller chunks of content to the user without having to process all the content and making it available for both offline use and for faster online experiences. 

Because web technologies (HTML, CSS and Javascript) and APIs (Service Workers, Push Notification and Background Sync) are at the core of these new experiences we gain all of the advantages of the open web platform and inherit its issues. We can rely on the web technologies we are familiar with and expand the reach of what we can do with and in browsers. 

In [Tablets are waiting for their Movable Type](https://signalvnoise.com/posts/3334-tablets-are-waiting-for-their-movable-type) the author wishes for a table equivalent to Movable Type wishing for their simplicity and ease of use out of the box (although from what I remember, customizing MT was a nightmare.) I posit that the web needs the Tablet equivalent to WordPress… easier to publish and customize. Using web content needs to be as complicated as you want it to be and we can leverage the strengths of the platform while minimizing their weaknesses.  How do we take this message out without turning it into another jQuery?

Ebooks have to live with a very long tail that makes it harder to experiment with. We have to contend with first generation hardware Kindle devices (the ebook equivalent to IE6) that are still potential customers for publisher's content. Even within the same format we have enough discrepancies to make it really hard to work on most (never mind all) devices that claim to support the spec.

The main advantage of the open web is the searchability and “the power of the URL”.  We no longer need an app store for our content (although having one can't hurt), we can just point users to Google (or your favorite search engine) and can search within the publication without depending on the native app search… if the search engine route is not enough then libraries like [lunr.js](http://lunrjs.com/) provides client-side search capabilities.  

And if this is not enough we can add further metadata inline using [resources](https://schema.org/Book) from [schema.org](https://schema.org/) to document the book and our own custom vocabularies based on existing .  Specialized search engines can then look for this metadata and display it in special ways; Google’s search engine already does this with enriched pages. 

Because we are no longer pushing heaps of content to our users every time we update the application we can update more frequently and, if the users agrees, transparently to them in a way that doesn’t preclude their using the content they already have… better to have old content than no content at all. 

We may decide that the subcompact model doesn’t work for the type of content we are trying to package. That’s ok too, the PSWAs can also handle traditional publishing paradigms where we push the entire contnt to the user all at once or in stages… for example we can download the first 10 chapters of our book when the Service Worker first installs and then load the rest of the book in chunks of 5 or 10 chapters as we update the service worker… the process will be as transparent to the user as we choose to make it

Where does the content come from?  One of the things I find most intriguing  are some of the way in which we create the content and turn it into a book, and even if that book making process is necessary at all.  There are three examples that I find particularly iluminating:

[Signal versus Noise](https://m.signalvnoise.com/) is 37 Signals (now Basecamp) blog. It has, directly or indirectly, spawned multiple books ([Getting Real](https://basecamp.com/about/books/Getting%20Real.pdf), [Remote](https://37signals.com/remote) and [Rework](https://37signals.com/rework).) I was particularly taken with the first book and how it translated content into book format.

I’ve mentioned [Art Space Tokyo](http://artspacetokyo.com/) and its multiplatform approach as one of the earliest influences on this project. It started me thinking about the differences between physical and digital and how they live together in a continuum rather than working against each other in an **either/or dichotomy**. This led me into considering the different formats that are available for online content, what are their advantages and how can the open web leverage these advanages when creating digital content.   

I reached some conclusions about the Digital Content We Make:

* The Digital Content We Make embraces their medium — working in concert with the content to make a clear and compelling narrative
* The Digital Content We Make does not need to obey the same constraints printed books do
* The Digital Content We Make is confident in the usage of digital tools and techniques. We’re not afraid to use polyfills and plugins to accomplish our goals

I’m ambivalent when it comes to paged content. One the one hand it’s hard to get out of the metaphors that correspond to what we do in the physical world but, on the other hand, how much do we really exercise those metaphors when reading online [if we read online at all](https://www.nngroup.com/articles/how-users-read-on-the-web/)?  How much of these scanning versus reading rules apply to our long form content? 

There are other things that attracted me to web publishing… I call it engaged readers.  Experiences like [Explorable Explanations](http://worrydream.com/ExplorableExplanations/) and [Parable of the Polygons](http://ncase.me/polygons/) are highly interactive experiences that requires Javascript libraries and scripts that are highly unlikely to be supported by ebook readers due to their security models. But we can do this and more in a web publication. It greatly expands what we can do with our books and makes it easier to convert paper books into fully interactive digital ones.  To me this is one more reason to move to the open web as the driver for our digital books. 

This will sound counterintuitive in light of the subject of these thoughts. KISS seems to be a forgotten art and we keep going for the latest and greatest and we forget that the biggest thing analog has over digital is its simplicity for the end user. 

<div class="video">
<iframe width="560" height="315" src="https://www.youtube.com/embed/UlzG6-fI00g?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

I think that’s the other reason for this project: to provide a set of technologies for digital web publishing while keeping the possibilities open.  Both progressive web applications and subcompact publishing promote simplicity for the end user. Choosing the technologies we use to deliver these experiences should be simple as well. 
