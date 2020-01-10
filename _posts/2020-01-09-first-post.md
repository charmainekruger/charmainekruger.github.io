---
layout: post
title: Tracking in an iFrame using Google Tag Manager
#subtitle: Each post also has a subtitle
tags: [gtm,google tag manager,iframe]
comments: true
---
The very first website I was tasked with to track, was riddled with iframes. I'm talking about iframes within iframes, the kind of stuff that I thought was only possible in the 2010 science fiction movie Inception.
Back then, there wasn't a lot of information available on this topic, so tracking of iframes very quickly became the bane of my existence. 

Fastforward to a couple of years later and I still regularly encounter client websites that use iframes and cause tracking nightmares. Since this topic is personal to me, I thought it very fitting to be the first topic to write about.


**Why is it so difficult to track iframes?**

An iframe is basically a website within a website.
If you only put an analytics tracking snippet on the outer (parent) website, you will be blind to the user interactions happening within the inner (iframe) website, as it iwill only track the page hit of the parent frame.
If you put the same tracking snippet on the iframe as well, you will get double page views and if the parent website and the iframes are on different domains, you will lose campaign or referral attribution of the hits.

**Is there a solution?**

Yes, there are a couple of solutions, I will discuss 2 of my favorite solutions below.

## Solution 1: postMessage

postMessage is a browser functionality that allows you to send messages between the iframe and the parent frame (main website).
When I started my measurement career, the postMessage API was not around, which left me stuck in my iframe-inception world, but these days browsers all support the postMessage API.

**Basic overview of the solution:**

* Send a message from the child iframe to the parent frame whenever a user interaction happened within the iframe. 
* Listen for the message from the child iframe on the parent frame. 
* Catch the message on the parent frame and push an event into the DataLayer.
* Propagate analytics hits.

**Step-by-step instructions:**

**Pro's:**
* postMessage provides a safe means of communication between frames on different domains while still offering protection from cross-site scripting attacks.
* Current browsers fully support the postMessage method.
* Tracking snippets on the iframe simply send messages to the main website. No page hits are fired from within the iframe, so tracking control all sits with the main (parent) website. This takes care of the double page view tracking as well as campaign/referral attribution issues.

**Con's**
*  You need to have access to add some tracking snippets to the HTML code of the iframe.


<iframe id="testIframe" src="/testPage.html"></iframe>


## Solution 2: customTask










Here's a code chunk:

~~~
<script type="text/javascript">
	document.querySelector('button').onclick = function () {
		// parent.postMessage("message to be sent", "http://the-website-that-will-receive-the-msg.com")
		parent.postMessage("myevent", "*")
	};
</script>
~~~

And here is the same code with syntax highlighting:

```javascript
<script>
(function(){
    try {
        if(typeof parent != "undefined" && parent != window) {
            if(typeof parent.postMessage != "undefined") {
                var message = {};
                message["origin"] = {
                    "type" : "iframe",
                    "host" : {{Page Hostname}},
                };
                var event = "custom.postMessage";
                // Add description of the event
                event += ".page";
                message["event"] = event;
                // Add custom data
                message["url"] = {{Page URL}};
                // Convent message into a string
                var messageJSON = JSON.stringify(message);
                //Send message to parent
                parent.postMessage(messageJSON, "*");
            }
        }
    } catch(err){if({{Debug Mode}}) console.log(err);};
})();
</script>
```

And here is the same code yet again but with line numbers:

{% highlight javascript linenos %}
var foo = function(x) {
  return(x + 5);
}
foo(3)
{% endhighlight %}

## Boxes
You can add notification, warning and error boxes like this:

### Notification

{: .box-note}
**Note:** This is a notification box.

### Warning

{: .box-warning}
**Warning:** This is a warning box.

### Error

{: .box-error}
**Error:** This is an error box.

