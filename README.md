# Progressive Subcompact Publications: An update and a proof of concept

I some times forget that I started something and don't realize that it's there until 5 years later like these ideas and proof of concepts. When I wrote the posts in this repo I was just starting to look at these (then) new tools and the technology landscape looked very different than today.

Techonologies like Web Components are still here but they are not as easily deployable as I once hoped because Apple and Mozilla decided that HTML imports were not good for the web and to wait for whatever modules bring into the discussion... we're still waiting.

While we wait, other technologies have appeared that show promise when it comes to working with book-like content on the web (and offline too).

## Deciding what we want to build

In building a proof of content we need to decide what things we want to validate. I think this is a good starting point for reading experiences on the web:

**Build once, run on every browser**. Whether it's installable or not the reading experience should remain consistent across devices.

**It uses standard web technologies**. Meaning that we only use published specs from one of the following groups:

* WICG
* WHATWG
* TC39
* CSS Working group.

**It has to run on modern rendering engines/browser combinations**. Whatever technology we choose for this POC must work on the three major rendering engines: Blink (Chrome and Chromium browsers), Gecko (Firefox), WebKit (Safari).

**It has to use HTML content**. Regardless of how we choose to build the content, it has to be HTML that will render fine without CSS and whether Javascript is available or not.

**It leverages progressive enhancements**. Content is the essential building block for our reading experiences so it also has to be the core starting block for creating the experiences; every thing we layer on top of the content is gravy that we can do without.

## Building the content

For the proof of concept I've picked Youth by Isaac Asimov (now in the public domain and [available from Project Gutenberg](http://www.gutenberg.org/ebooks/31547)) to test and validate the code for this project.

### Manifest

The first item to consider when building a PWA is the manifest. It does several things that will make browsers consider the site as a PWA

### Service Worker

#### Precache

#### Different routes for different types of content

#### Note when new content is available

### Content

### CSS

#### To grid or not to grid

#### Typography and fonts

#### Print options?

### Javascript (if any)

### HTML

## Packaging the content for delivery

### Ensuring Installability in Chrome for desktop

### Creating an Unsigned Web Bundle

## Looking forward

## Links and Resources

* IDPF and W3C Merger
  * [Books, in a browser](https://medium.com/@naypinya/books-in-a-browser-375df76207ce#.llmtpb7oo)
  * [W3C and IDPF: Better Together?](https://medium.com/@dauwhe/w3c-and-idpf-better-together-c92988674444#.sz3sennm4)
    * [Baldur Bjarnason’s response](https://medium.com/@fakebaldur/my-first-short-response-to-you-on-this-idpf-w3c-thing-a53a028fafd#.hbin1cjvb)
    * [One question about ebook-related standardisation at the W3C](https://medium.com/@fakebaldur/one-question-about-ebook-related-standardisation-at-the-w3c-d45aa2bd1b52#.kw726kvsi)
* Frank Chimero
  * [The Web's Grain](https://frankchimero.com/blog/2015/the-webs-grain/)
  * [What The Web Wants](https://frankchimero.com/blog/2013/what-screens-want/)
* Apps and publishing
  * [Post artifact books and publishing](http://craigmod.com/journal/post_artifact/)
    * [Newspapers and Thinking the Unthinkable](Newspapers%20and%20Thinking%20the%20Unthinkable%0A)
    * [On Wikipedia, Cultural Patrimony, and Historiography](http://booktwo.org/notebook/wikipedia-historiography/)
      * You own your content
        * [Amazon zaps purchased copies of Orwell's 1984 and Animal Farm from Kindles](http://boingboing.net/2009/07/17/amazon-zaps-purchase.html)
      * [Forever](http://aworkinglibrary.com/writing/forever/index.html) - Things never last forever on the web
      * [The social life of marginalia](http://bobulate.com/post/5013829096/the-social-life-of-marginalia)
        * Bookmarking, annotations and sharing
          * [openbookmarks.org](http://www.openbookmarks.org/)
          * [Hypothes.is](https://hypothes.is/)
          * [emphasis](https://github.com/NYTimes/Emphasis) from the New York Times
  * [Subcompact Publishing](https://craigmod.com/journal/subcompact_publishing/)
  * [What books will become](http://kk.org/thetechnium/what-books-will/)
    * [As we may read](http://craigmod.com/sputnik/as_we_may_read/) and [Future Reading](https://aeon.co/essays/stagnant-and-dull-can-digital-books-ever-replace-print)
  * [Our new shrines](http://contentsmagazine.com/articles/our-new-shrines/)
    * [How apps are made](http://contentsmagazine.com/articles/our-new-shrines/)
  * [ebooks for all](http://craigmod.com/sputnik/worldreader/)
  * [Pull back](https://medium.com/@craigmod/pull-back-17a9ebc060d3#.lv3ix13z9)
  * [Unbinding and edges](https://craigmod.com/essays/unbinding/)
    * [A Turn of the Page for Newsweek](http://www.thedailybeast.com/articles/2012/10/18/a-turn-of-the-page-for-newsweek.html)
    * [The digital - physical](http://craigmod.com/journal/digital_physical/)
      * [Christo and Jeanne-Claude: Gentle Disturbances](http://www.artagogo.com/commentary/christo/christo.htm)
      * [The Umbrellas](http://christojeanneclaude.net/projects/the-umbrellas)
  * [Publishing Startups and the Great Fuzziness](http://craigmod.com/sputnik/publishing_startups/)
  * [Platforming books](http://craigmod.com/journal/platforming_books/)
    * [Amazon: Kindle Ebooks Now Outsell All Paper Books Combined](http://www.cultofmac.com/182864/amazon-kindle-ebooks-now-outsell-all-paper-books-combined/)
  * [Hack the cover](http://craigmod.com/journal/hack_the_cover/)
    * [Has Kindle Killed the Book Cover?](Has%20Kindle%20Killed%20the%20Book%20Cover?)
    * [Scan this book!](http://www.nytimes.com/2006/05/14/magazine/14publishing.html?pagewanted=all)
    * [The End of Authorship](http://www.nytimes.com/2006/06/25/books/review/25updike.html?pagewanted=print&pagewanted=all)
    * [The book cover archive](http://bookcoverarchive.com/)
    * [Why aren’t there words in the cover of our books?](http://www.thedominoproject.com/2011/02/why-arent-there-words-on-the-cover-of-our-books.html)
  * [The shape of our future book](http://craigmod.com/sputnik/our_future_book/)
  * [Bookcubes: souvenirs of Digital Reading](http://booktwo.org/notebook/bookcubes/)
  * [Subcompact Publishing](http://craigmod.com/journal/subcompact_publishing/)
    * [Clay Christensen on the news industry: “We didn’t quite understand…how quickly things fall off the cliff”](http://www.niemanlab.org/2012/10/clay-christensen-on-the-news-industry-we-didnt-quite-understand-how-quickly-things-fall-off-the-cliff/)
  * [Subcompact roundup and The Daily](http://craigmod.com/sputnik/subcompact_round_up/)
    * [Tablets are waiting for their Movable Type](https://signalvnoise.com/posts/3334-tablets-are-waiting-for-their-movable-type)
    * [A new model for the musician-to-listener relationship](https://medium.com/the-future-of-publishing/a-new-model-for-the-musician-to-listener-relationship-44e778827320#.u22ff8gs9)
    * [Sub-compact media: Rethinking the way we publish online](https://gigaom.com/2012/11/30/sub-compact-media-rethinking-the-way-we-publish-online/)
    * [Get ready for the age of premium micropublishing](https://pando.com/2012/12/03/get-ready-for-the-age-of-premium-micropublishing/)
    * [Trend alert: small internet publications](http://kottke.org/12/12/trend-alert-small-internet-publications)
    * [A Response to ‘Subcompact Publishing’](http://www.foliomag.com/2012/response-subcompact-publishing/#.UMET28pU7jL)
    * [How To Publish a "Minimum Viable Magazine" Online](https://www.technologyreview.com/s/508166/how-to-publish-a-minimum-viable-magazine-online/)
    * [Some lessons from the demise of The Daily: Was it the platform, the content, the structure, or the business model?](http://www.niemanlab.org/2012/12/some-lessons-from-the-demise-of-the-daily-was-it-the-platform-the-content-the-structure-or-the-business-model/)
* Technology
  * Progressive Web Appllications
    * Background
      * [Progressive Web Apps: Escaping Tabs Without Losing Our Soul](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/)
      * [Service Workers and PWAs: It’s About Reliable Performance, Not “Offline”](https://infrequently.org/2016/05/service-workers-and-pwas-its-about-reliable-performance-not-offline/)
    * How to
      * [Your First Progressive Web App](https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/?hl=en)
      * [Why Progressive Web Apps Are The Future Of Web Development](https://arc.applause.com/2015/11/30/application-shell-architecture/)
    * Forward thinking
      * [What Progressive Web Apps Mean for the Web](http://developer.telerik.com/featured/what-progressive-web-apps-mean-for-the-web/)
      * [Are Progressive Web Apps the Future?](http://developer.telerik.com/featured/are-progressive-web-apps-future/)
  * Application Shell Architecture
    * [Instant Loading Web Apps with An Application Shell Architecture](https://developers.google.com/web/updates/2015/11/app-shell)
    * [Architect the App Shell](https://developers.google.com/web/fundamentals/getting-started/your-first-progressive-web-app/step-01?hl=en)
    * [Instant Loading Web Apps With An Application Shell Architecture](https://medium.com/google-developers/instant-loading-web-apps-with-an-application-shell-architecture-7c0c2f10c73#.w7icv2hw9)
    * Examples
      * [iFixIt PWA](https://ifixit-pwa.appspot.com/)
      * [Service Workers in Production: Google I/O 2015 app](https://developers.google.com/web/showcase/case-study/service-workers-iowa)
  * Service Workers
    * [Is ServiceWorker Ready](https://jakearchibald.github.io/isserviceworkerready/)
    * [Service Workers Sepcification](https://www.w3.org/TR/service-workers/) at W3C
  * Examples and tutorials
    * Jake Archibald
      * [Offline Cookbook](https://jakearchibald.com/2014/offline-cookbook/)
      * [Simple Service Worker tutorial](https://github.com/jakearchibald/simple-serviceworker-tutorial)
    * Making A Service Worker: A Case Study
      * [Smashing Magazine Article](https://www.smashingmagazine.com/2016/02/making-a-service-worker/)
      * [Accompanying code](https://github.com/lyzadanger/serviceworker-example)
    * The Guardian
      * [Building an offline page for Guardian.com](https://www.theguardian.com/info/developer-blog/2015/nov/04/building-an-offline-page-for-theguardiancom)
    * Jeremy Keith’s Example
      * [My first Service Worker](https://adactio.com/journal/9775)
    * Nicolás Bevacqua
      * [ServiceWorker: Revolution of the Web Platform](https://ponyfoo.com/articles/serviceworker-revolution)
    * Vendor Examples
      * [Service Worker Samples](https://github.com/GoogleChrome/samples/tree/gh-pages/service-worker) (Google)
      * [The Service Workers Cookbook](https://serviceworke.rs/) (Mozilla)
        * [Caching from a zip file](https://serviceworke.rs/cache-from-zip_worker_doc.html) (mozilla)
  * Tools
    * From Google
      * [Workbox.js](https://developers.google.com/web/tools/workbox)
  * Things that sit on top of service workers
    * [Background Sync](https://github.com/slightlyoff/ServiceWorker/blob/master/explainer.md)
    * Push Notifications
      * [Specification](https://www.w3.org/TR/push-api/) at W3C
      * Current Implementations
        * [Chrome](https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web?hl=en)
    * [Background Sync with Service Workers](https://davidwalsh.name/background-sync)
    * [Periodic Background Sync](https://web.dev/periodic-background-sync/)
    * [Update Broadcasts](https://web.dev/broadcast-updates-guide/)
* Other digital reading experiences
  * [Pelican Books](https://www.pelicanbooks.com) → read online (Unreachable Host)
    * [How Pelican Books is reimagining the cover in the digital age of eBooks](http://www.fastcodesign.com/3038698/how-pelican-books-is-reimagining-the-cover-in-the-age-of-e-books)
    * [Pelican Books: an unrivalled online reading experience](https://www.creativereview.co.uk/cr-blog/2014/november/pelican-books-an-unrivalled-online-reading-experience/)
  * [The Shape of Design](http://shapeofdesignbook.com) by Frank Chimero
    * [Designing in the borderlands](http://www.frankchimero.com/writing/designing-in-the-borderlands/)
  * Butterick’s [Practical Typography](http://practicaltypography.com)
    * The publishing system that powers the book: [Pollen](http://docs.racket-lang.org/pollen/)
  * [Hi](https://hi.co/) (No longer active)
    * [Background](https://craigmod.com/essays/hitotoki_narrative_mapping/)
  * [Art Space Tokyo](http://read.artspacetokyo.com/)
    * [Platforming Books](http://craigmod.com/journal/platforming_books/)
  * [Pubpub](https://www.pubpub.org/)
