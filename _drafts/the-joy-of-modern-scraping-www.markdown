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
Cheerio{%include footnote_ref.html number="1" %}:

{% include figure.html img="/assets/2018-05-01-the-joy-of-modern-scraping-www/email-1.png"
caption="Scraping a <a href=\"https://en.wikipedia.org/wiki/JavaServer_Pages\">JSP</a> generated <a href=\"https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp\">web page</a>: how hard it could be?"
alt="Hi I found you on this url https://github.com/cheeriojs/cheerio/issues/786
Can you try to and see if you can scrap the table data of this url https://www3.bcb.gov.br/selic/portal-selic-servicos/pesquisa-taxa-apurada.jsp

I am trying to to scrap with nodejs request and cherio but I get no content when I fetch the table. Maybe it's because the request is being made before the page being is rendered they are using java and the page takes a while to get rendered. I dont know.

Any help?" %}

#### Reality is more complex than it seems *- Susan West*

I told myself:

> Ok, this guy just don't know how to use Cheerio, I'll show him!

So here I go:

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
caption="Things aren't working very well."
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

TODO: execution sample.

Ok. Actually this guy know how to use Cheerio. That's not that. This time I'm curious!

So, what's wrong there? The data is here in our page, we iterate over the grid cells content and log it. Why damn can't we just get this data on our console?

#### From complacency to compassion

I found this complaint absolutely based: if we see the data in the
web browser, why can't we just scrap it from the HTML returned by the web server?
Doesn't the browser display this HTML?

After all if you load the page you could expect to find what you see.

Or it isn't?

Well, I may be an old boy to have these expectations. Finally I thought about it a little more -
and it all becomes clear:

> That's right! We're not anymore in the 2000's!


--------------------------

TODO: style the . Use main accent color?

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
