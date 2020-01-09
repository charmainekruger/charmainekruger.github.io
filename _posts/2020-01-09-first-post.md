---
layout: post
title: Tracking in an iFrame using Google Tag Manager
#subtitle: Each post also has a subtitle
tags: [gtm,google tag manager,iframe]
comments: true
---
When installing Analytics tracking code on a site that uses iframes, extra care needs to be taken to ensure that traffic is tracked accurately. 
The 2 most common errors with tracking iframes I have encountered so far is:

{: .box-error}
**Error:** recording double page views and not being able to retain campaign or referral attribution.

Google Analytics will track all page hits by default, but in the case of an iframe (child frame), it will only track the page hit of the main website (parent frame) and all user behavior within the iframe will be lost. An iframe is basically a website within a website.

**What do you need to track user behavior within the iframe?**
*  You need to have access to add some tracking snippets to the HTML code of the iframe.
*  You need to send a message from the child iframe to the parent frame whenever a user interaction happened within the iframe.
*  You need to listen for the message from the child iframe on the parent frame.
*  The parent frame needs to catch the message and push an event into the DataLayer.


**Sending messages from the child iframe to the parent webpage**

The solution is postMessage. This supported browser functionality allows you to send messages between both the iframe and parent window/main website, in most cases circumventing all the problems you would face traditionally with tracking iframes.
The great aspect of this solution is that the iframe simply sends messages to the main website, no tracking scripts are called within the iframe. The main website, where the tracking takes place, will not encounter session, attribution or access issues, while providing more control of when to fire specific tracking calls for the iframe content..

Cross-Document Messaging with postMessage is designed to provide a safe means of communication between documents on different domains while still offering protection from cross-site scripting attacks. This method can be used with iframes as well as between windows when the window.open method is used by one to open another.

Current browsers fully support the postMessage method.





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
