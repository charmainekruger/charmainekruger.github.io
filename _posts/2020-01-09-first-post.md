---
layout: post
title: Tracking in an iFrame using Google Tag Manager
#subtitle: Each post also has a subtitle
tags: [gtm,google tag manager,iframe]
comments: true
---
When installing Analytics tracking code on a site that uses iframes, extra care needs to be taken to ensure that traffic is tracked accurately. 
The 2 most common problems with tracking iframes I have encountered so far is:
1.  recording double page views and 
2.  not being able to retain campaign or referral attribution.

Google Analytics will track all page hits by default, but in the case of an iframe, it will only track the page hit of the parent frame and all user behavior within the iframe will be lost. An iframe is basically a website within a website.

**What do you need to track user behavior within the iframe?**
*  You need to have access to add some tracking snippets to the HTML code of the iframe.
*  You need to "post" a message from the iframe to the parent frame whenever a user interaction happened within the iframe.
*  You need to listen for the message on the parent frame.
*  The parent frame needs to catch the message and push an event into the DataLayer.



In order to track user interactions happening within the iframe, you need to have access to the HTML code of the iframe.





Here's a code chunk:

~~~
var foo = function(x) {
  return(x + 5);
}
foo(3)
~~~

And here is the same code with syntax highlighting:

```javascript
var foo = function(x) {
  return(x + 5);
}
foo(3)
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
