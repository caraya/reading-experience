# reading-experience

There has been a lot of talk and discussion on where ebooks and digital publishing is going, Whether PWP will survive the standardixation process at the W3C or will go the way of XHTML2 Dodo and be consigned to infamy. 

I want to take a different approach to the issue.  I still think that the technologies behind [Progressive Web Applications](https://infrequently.org/2015/06/progressive-apps-escaping-tabs-without-losing-our-soul/) are the best answer currently available for creating web content that is engaging and leverages the strength of the web over reader preferences (I know, sacrilege.) The principles of what make an app progressive:

* Responsive: to fit any form factor
* Connectivity independent: Progressively-enhanced with Service Workers to let them work offline
* App-like-interactions: Adopt a Shell + Content application model to create appy navigations & interactions
* Fresh: Transparently always up-to-date thanks to the Service Worker update process
* Safe: Served via TLS (a Service Worker requirement) to prevent snooping
* Discoverable: Are identifiable as “applications” thanks to W3C Manifests and Service Worker registration scope allowing search engines to find them
* Re-engageable: Can access the re-engagement UIs of the OS; e.g. Push Notifications
* Installable: to the home screen through browser-provided prompts, allowing users to “keep” apps they find most useful without the hassle of an app store
* Linkable: meaning they’re zero-friction, zero-install, and easy to share. The social power of URLs matters.

There is another piece to the equation. About 4 years ago, Craig Mod published [Subcompact Publishing](http://craigmod.com/journal/subcompact_publishing/) as both a manifest and an idea of what could digital publishing look like. The ethos is simple and covers those areas not covered under the technical aspect of progressive web apps:

* Small issue sizes (3-7 articles or chapters / per issue or download)
* Small file sizes
* Digital-aware subscription prices
* Fluid publishing schedule
* Scroll (don’t paginate)
* Clear navigation
* HTML(ish) based
* Touching the open web

Grante
