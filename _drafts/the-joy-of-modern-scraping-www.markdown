---
layout: post
author: yoan
title: "The Joy of Modern Scraping: from World Wide Web to Wery Wide Webapps"
categories: [Web]
tags: [Scraping, Web, Node, HTTP, HTML, PhantomJS, jQuery]
brief: "From the 90s paradise of plain-good-old raw HTML pages to the evils of Javascript webapps."
lovehatefeedback: true
draft: true
---

The other day, someone hit my mail, expecting I would know some shit about
Cheerio{%include footnote_ref.html number="1" %}.

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/email-1.png"
caption="Scraping a <a href=\"https://en.wikipedia.org/wiki/JavaServer_Pages\">JSP</a> generated <a href=\"https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp\">web page</a>: how hard it could be?"
alt="Hi I found you on this url https://github.com/cheeriojs/cheerio/issues/786
Can you try to and see if you can scrap the table data of this url https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp

I am trying to to scrap with nodejs request and cherio but I get no content when I fetch the table. Maybe it's because the request is being made before the page being is rendered they are using java and the page takes a while to get rendered. I dont know.

Any help?" %}

I told myself:

> Ok, this guy just don't know how to use Cheerio, I'll show him!

#### A story of Interests

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/email-2.png"
caption="If it doesn't work with jQuery, it won't with Cheerio: let's start there. You see, it just works!
The indices are Brazilian Central Bank (BCB) interest rates."
alt="Hello Alan!

Does your scraping code work when you execute it in the browser? (in the browser Dev Tools console)

For me it give some results:
$('.ui-grid-row .ui-grid-cell').each(function(i,row) {
	console.log($(row).find('.ui-grid-cell-contents').text());
})

Gives:
> 09/02/2018
> 6.65
> 1,00025552
> 415,793,650,995.29

How does your Node code loads the page before you try to parse it with Cheerio?

Good luck,
Yoan

PS. What are these indices?" %}

Well, actually, maybe it isn't that easy:

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/email-3.png"
caption="It should work, no?"
alt="Hi Yoan,

Thanks for your quick reply. In fact my code was much the same as yours. I just hadn't test from console. My code uses Node request module to fetch the url to be scrapped. And it actually works. But I guess the table row is created after the cheerio object is created, that's why it returns anything.

Here's my Node code:

var request = require('request');
var cheerio = require('cheerio');

request('https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp', function (error, response, html) {
  if (!error) {
    var $ = cheerio.load(html);
    $('.ui-grid-row .ui-grid-cell').each(function(i,row) {
	  console.log($(row).find('.ui-grid-cell-contents').text());
    })
  }
});" %}

Ok, actually this guy know how to use Cheerio: that's not the issue. This time I'm curious!

#### Reality is more complex than it seems *- Susan West*

So let's recap. If we execute this jQuery selector directly in our browser Dev Console:

{% highlight javascript %}
console.log(`Selector matched ${$('.ui-grid-row .ui-grid-cell').length} element(s).`);
$('.ui-grid-row .ui-grid-cell').each(function(i,row) {
	console.log($(row).find('.ui-grid-cell-contents').text());
});
{% endhighlight %}

This gives us:

{% highlight shell %}
Selector matched 5 element(s).
30/04/2018
6.40
1,00024620
280,695,996,937.26
{% endhighlight %}

Adding some debug to Alan snippet, now in Node, parsing the same page with Cheerio:

{% highlight javascript %}
var request = require('request');
var cheerio = require('cheerio');

console.log('Loading the web page...')
request('https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp', function (error, response, html) {
    if (!error) {
        console.log('Loaded HTML. Parsing with Cheerio...');
        var $ = cheerio.load(html);
        console.log(`Selector matched ${$('.ui-grid-row .ui-grid-cell').length} element(s).`);
        $('.ui-grid-row .ui-grid-cell').each(function(i,row) {
            console.log($(row).find('.ui-grid-cell-contents').text());
        })
    }
});
{% endhighlight %}

Which gives us:

{% highlight shell %}
$ node test-scrap.js
Loading the web page...
Loaded HTML. Parsing with Cheerio...
Selector matched 0 element(s).
{% endhighlight %}

So, what's wrong here?

We fetch the page, parse it using Cheerio, we iterate over the grid cells content and log it. Why damn can't we just get this data on our console?

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/giphy-cat-laser.gif"
caption="Things aren't working very well. Source: <a href=\"https://giphy.com/gifs/cats-amazing-lasers-Sn2OnNycOmVG0\">Giphy</a>."
alt="Cat after a laser light: never catching it" %}


#### From complacency to compassion

The complaint seems absolutely founded for me: if we see the data in the
web browser, why can't we just scrap it from the HTML returned by the web server?
Doesn't the browser display this HTML? Why it is not acting the same?

After all if you load *the* page you could expect to find what you see on *the* page.

Or it isn't?

Finally I thought about it a little more - and it all becomes clear:

> That's right: we're not anymore in the 2000's!

If we really look at [the HTML page as returned](https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp) by the server, we encounter the frightening
true: the HTML page that we got is not the page that we see live in the browser. Here an extract:

{% highlight html %}
<div class="container75">
	<div class="containerGrid" id="taxaSelic" ng-show="vm.grid.totalItems > 0">
		<div class="gridPesquisa taxa_apurada_grid" ng-style="vm.grid.obterAlturaPagina()" ng-show="vm.grid.totalItems > 0" ui-grid="vm.grid" ui-grid-edit ui-grid-auto-resize ui-grid-pagination></div>
	</div>
</div>
{% endhighlight %}

Here should be our grid. There is no grid. We only found our outer HTML elements,
with some weird attributes. It's now clear why our Node script can't extract the data:
it is not here. [Angular 1](https://docs.angularjs.org/api/ng/directive/ngShow)

#### Your Dad Internet

Good old dad internet
When jQuery was high-tech, PHP was king. Flashy colors, ever [spinning animations](http://www.ifindit.com/),
[texts running away](http://web.archive.org/web/20071012164808/http://www.beuzeville.fr/)

Look at the Cat Explorer in the formidable Window93 for what it really looked like. https://www.windows93.net/#!catex

The grand expectation:

> All rendered elements and content is in the main HTML resource.

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/old-web-beuzeville.png"
caption="In those days, all data was in the HTML resource returned by the web server."
url="http://web.archive.org/web/20120330195406/http://www.beuzeville.fr/"
alt="Cat after a laser light: never catching it" %}


The only external resources mentioned by the first formal HTML standard ([RFC 1866](https://tools.ietf.org/html/rfc1866) Note: now standardized by IETF/W3C group)
are images:

> An HTML user agent allows users to interact with resources which have
   HTML representations. At a minimum, it must allow users to examine
   and navigate the content of HTML level 1 documents. HTML user agents
   should be able to preserve all formatting distinctions represented in
   an HTML document, and be able to simultaneously present resources
   referred to by IMG elements (they may ignore some formatting
   distinctions or IMG resources at the request of the user). Level 2
   HTML user agents should support form entry and submission.

You wouldn't go really further in the browser at this time. Even interactive menus
were on the risky side of what you could do *in* the browser. Who would have think
to load data asynchronously?

The [`XMLHttpRequest`](https://en.wikipedia.org/wiki/XMLHttpRequest) object was a
non-standardized and sometimes-working API until 2005. Gmail was maybe the first
real-world, large scale example of the utility of such [Ajax](https://en.wikipedia.org/wiki/Ajax_(programming)) views. Complex web apps may have
been existing [since a long time](http://www.paulgraham.com/first.html), but they still
heavily relied on the server to prepare a near-complete HTML page.

We have come a long way.

#### The advent of Async Web Apps

Webapps days
Node and NPM [fanboys](https://www.youtube.com/watch?v=bzkRVzciAZg)
Angular opened the way for [single-page applications](https://en.wikipedia.org/wiki/Single-page_application)
React consecrated it.
Ajax by itself no fun anymore. `XMLHttpRequest` disappearing for `fetch`.
Rest API are not even anymore good (GraphQL)

Who would expect data to be returned from the HTML page deserved by the web server?

--------------------------

TODO: style the quotes. Use main accent color?

Well, I may be an old boy to have these expectations.

#### Scraping the wild

Progressive:
* raw scraping; not that easy anymore;
* you need a full webbrowser to get any actual modern web page _content_.

Is there still such a thing that server-rendered HTML web pages?

https://github.com/MonsieurV/node-scraping-examples

<br>

##### Notes

{% include footnote.html number="1" note="Not remotely true. [Cheerio](https://github.com/cheeriojs/cheerio) is a jQuery implementation for Node. I do know how to use a jQuery selector."  %}

<!-- {% include footnote.html number="1" note="Not remotely true. [jsdom](https://github.com/jsdom/jsdom) is a pure Node implementation of WhatWG [DOM](https://dom.spec.whatwg.org/) and [HTML](https://html.spec.whatwg.org/) standards (that is an headless web browser coded in Javascript, aka a browser without an UI and that is not based on any mainstream browser). It is a fairly complex and oppressive piece of software, to say the least."  %} -->
