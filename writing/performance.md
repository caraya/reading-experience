# How have reader expectations changed? (UI for the performance obsessed)
as consumers of information we’ve all become more demanding. We want the content faster and we want to be engaged with the content we access online.  PSPs should be no different in their performance to traditional web applications. 

Rather than provide a one-size-fits-all solution I present some of the data Nielsen first articulated in 1993’s *Usability Engineering* We’ll use these values to draw some basic conclusions that will start the thinking about performance and how PSPs can work towards achieving those performance goals.

* **0.1 seconds** — Operations that are completed in 100ms or fewer will feel instantaneous to the user. This is the gold standard that you should aim for when optimising your websites.
* **1 second** — Operations that take 1 second to finish are generally OK, but the user will feel the pause. If all of your operations take 1 second to complete, your website may feel a little sluggish.
* **10 seconds** — If an operation takes 10 seconds or more to complete, you’ll struggle to maintain the user’s attention. They may switch over to a new tab, or give up on your website completely. Of course this depends on what operation is being completed. For example, users are more likely to stick around if they’ve just submitted their card details in the checkout than if they’re waiting to load a product page.
* **16 milliseconds** — Given a screen that is updating 60 times per second, this window represents the time to get a single frame to the screen (1000 ÷ 60 = ~16). People are exceptionally good at tracking motion, and they dislike it when their expectation of motion isn’t met, either through variable frame rates or periodic halting.

We want to get to the site’s first meaningful paint on initial load in as close to 1000 milliseconds as possible. 

Animations should take no more than 16 milliseconds in order to reach 60 frames a second.

## Performance Optimizations
We can optimize our resources so that time to first meaningful interaction is as short as possible. First load will also cache the resources needed for our application shell and then dynamically .  We want to optimize this to last as little as possible.  

This is not just speech for the sake of speach. Take the following graphic (from Soasta’s [Page bloat update: The average web page is more than 2 MB in size](#)(https://www.soasta.com/blog/page-bloat-average-web-page-2-mb/)).  How can we minimize the number of resources to load and cache the first time we access a page? How many of these resources can be reused on pages accross the site? How can we optimize images to reduce their size?

![](https://www.soasta.com/wp-content/uploads/2015/06/page-bloat-images.png)

We have gotten lazy or, possibly, made the wrong assumptions. The graphic below, also from Soasta’s blog post, show how the size of our web content has changed over the years… and it shows no signs of decreasing. 

![](https://www.soasta.com/wp-content/uploads/2015/06/page-bloat-May15-page-composition.png)

For a more complete perspective checkout Udacity’s [Website Performance Optimization](https://www.udacity.com/course/website-performance-optimization--ud884)course. 

## How are you serving your content?

PSPs do not avoid performance requirements... however the questions are slightly different. Are you serving your content with HTTP 1.x or HTTP2? When I first heard the question asked I laughed… why should this matter?

HTTP2 has changed the way we work with web content. What we used to consider as patterns and best practices are no longer necessay and may be considered anti patterns.  

Because of the way HTTP2 serves the content it is not necessary to concatenate our CSS and Javascript, it actually work better if you work with individual minimized files; HTTP2:

* is binary, instead of textual: Binary protocols are more efficient to parse, more compact “on the wire”, and most importantly, they are much less error-prone, compared to textual protocols like HTTP/1.x, because they often have a number of affordances to “help” with things like whitespace handling, capitalization, line endings, blank lines and so on
* is fully multiplexed: Multiplexing addresses [head-of-line-blocking](#)(https://www.wikiwand.com/en/Head-of-line_blocking) by allowing multiple request and response messages to be in flight at the same time; it’s even possible to intermingle parts of one message with another on the wire
* can use one connection for parallelism: One application opening many connections simultaneously breaks a lot of the assumptions that TCP was built upon; since each connection will start a flood of data in the response, there’s a real risk that buffers in the intervening network will overflow, causing a congestion event and retransmits
* uses header compression to reduce overhead:  If you assume that a page has about 80 assets and each request has 1400 bytes of headers, it takes at least 7-8 round trips to get the headers out “on the wire”. In comparison, even mild compression on headers allows those requests to get onto the wire within one round trip – perhaps even one packet.
* allows servers to “push” responses proactively into client caches: When the server pushes the content that the client will need before it needs it we can save networks requests by caching the content in the browser (HTTP) cache 

This doesn’t mean we shouldn’t minimize and compress resources but bundling them together is less important now that we don’t have to be concerned with saturating the connection to the server and can leverage HTTP2 push to let the server prefetch content to the client.  

Improving performance is a never ending game. Ilya Grigorik illustrates this point in his presentation from I/O 2016. 

<div class="video">
<iframe width="560" height="315" src="https://www.youtube.com/embed/aqvz5Oqs238?rel=0" frameborder="0" allowfullscreen></iframe>
</div>

## The proof is in the pudding

How many of you test your content with Dev Tools emulation mode? You should, as Dev Tools provides a suite of tools like network throtling and the ability to save the traces to share with your co-workers and others in your development team. THat said, it's not always enough.

Even though we have a simulator that can give us a pretty good simulator for both device sizes and bandwidth, it's still a simulation and it will work better than the actual devices.  If you run the content through your device or even with [Chrome Remote Debugging for Android](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/) and iOS when the device is connected to your laptop or desktop machine will give you a much better picture of how your application performs on your target devices. 

This is particularly important when working with features phone that are a popular, and some times the only, device people use when accessing the web. The devices are much slower and bandwidth is considerably more expensive than what we’re used to… For some people in India accessing a 2MB page will use up to 20% of their monthly bandwidth allotment. 

