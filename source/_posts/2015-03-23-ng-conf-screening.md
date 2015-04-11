title: ng-conf screening
date: 2015/03/23 12:00
tags: [angular,a11y,protractor,vzdělávání]

---

To share some thoughts about ng-conf and life in general we organized a little get-together at the Krakovska premises. With the managerial help of Honza and PR skills of Bob, we had quite a few colleagues attending, some also remotely (guys, did we at least show you all the food and drinks ?).

<!-- more -->

To cheer ppl up we started with the phenomenal [ng-wat](https://www.youtube.com/watch?v=M_Wp-2XA9ZU). For me personally it was an introduction to the wat concept in general. I lead a very culturally deprived life thus far. We followed somehow logically with the talk that followed at ng-conf as well - [Julie Ralph](https://twitter.com/SomeJulie) talking about [testing #a11y](https://www.youtube.com/watch?v=_2Pt6Xx94Bc) (accessibility) with angular.

To present what the [Accessibility audit](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en) results mean in real life, i was showing the [page we are working on](http://recipes-plus.co.uk/) with the [ChromeVox](https://chrome.google.com/webstore/detail/chromevox/kgejglhpjiefppelpmljglcjbhoiplfn?hl=en) screen reader on. The results were … slightly sad. I hope we will manage to improve the site so that it’d follow some of the most important advice and thus be way more readable to those with disabilities. This is not only important because we want to be nice people, but also for business :). With [ngAria](https://docs.angularjs.org/api/ngAria) (since Angular 1.3) this has become even easier. One just has to follow the rules as suggested by the audit & other tools. With **Protractor**, this can be even automated and improved in the future. The video also quickly introduced us to the plugin architecture in **Protractor** (in since 1.5) and how to build one.

The other director’s pick for our little ng-conf was in mood of Sasqwatch hunting. If you are not familiar with it, you should definitely check [this one](https://www.youtube.com/watch?v=wbcJfg-d5nI&list=PLOETEcp3DkCoNnlhE-7fovYvqwVPrRiY7&index=16)). Sasqwatches are those angular watchers, which can dramatically slow down your application and you perhaps don’t even know about them, as they are added automatically when you use a mustache template. William Scott Moss - the guy presenting - will tell you how to improve your angular app coding (templates especially) and make your app way faster. It is a must see [video](https://www.youtube.com/watch?v=wbcJfg-d5nI&list=PLOETEcp3DkCoNnlhE-7fovYvqwVPrRiY7&index=16)!!!

**More resources on accessibility:**

[Julie’s slides](http://t.co/7ZSnfszIuV)

[http://tenon.io]() - a service that provides more #a11y checks

**blogs on #a11y**

[http://www.paciellogroup.com/]()

[http://www.deque.com/blog/]()
