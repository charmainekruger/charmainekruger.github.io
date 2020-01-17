---
layout: post
title: Tracking in an iFrame using Google Tag Manager
#subtitle: Each post also has a subtitle
tags: [gtm,google tag manager,iframe]
bigimg: "/img/iframe1.jpeg"
comments: true
---
The very first website I was tasked with to track, was riddled with iframes. I'm talking about iframes within iframes, the kind of stuff that I thought was only possible in the 2010 science fiction movie Inception.
Back then, there wasn't a lot of information available on this topic, so tracking of iframes very quickly became the bane of my existence. 

Years later and I still regularly encounter client websites that use iframes and cause tracking nightmares. Since this topic is personal to me, I thought it very fitting to be the first topic to write about.


**Why is it so difficult to track iframes?**

An iframe is basically a website within a website.
If you only put an analytics tracking snippet on the outer (parent) website, you will be blind to the user interactions happening within the inner (iframe) website, as it will only track the page hit of the parent frame.
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

As an example I have created a simple iframe that pulls in a webpage that is hosted on a different domain as source.

<iframe id="testIframe" src="https://phillip-kruger.github.io/testPage.html"></iframe>

I already have Google Tag Manager and Google Analytics implemented on my main website.

1. Create a new Google Tag Manager container for your iframe (you can use the same container or a different container, my personal preference is keeping them in separate containers in the same account).
2. Add the GTM container snippet you created above to your iframe according to [these instructions](https://developers.google.com/tag-manager/quickstart). 
3. In the new iframe GTM container, create a new tag that fires when a specific user action happens within the iframe. In this scenario, I want to track when the button with id = "myButton" is clicked from within the iframe.

{: .box-note}
**Tag Configuration:**<br> Tag type: Custom HTML <br> Trigger: Click - All Elements, Click ID contains "myButton" <br> HTML code: See code box below <br>

```javascript
<script>
(function(){
  try {
    if(typeof parent != "undefined" && parent != window) {
    	if(typeof parent.postMessage != "undefined") {
         var postMessage = JSON.stringify({
            type: 'iFrame',
            event: 'iFrameButtonSubmitted',
            host: '{{Page Hostname}}'
          })
          parent.postMessage(postMessage, 'https://charmainekruger.github.io');
        }
    }
  } catch(e){
    if({{Debug Mode}}) 
    	console.log(e);
  };
})();
</script>
```
Apart from the custom HTML tag you created above, don't implent anything else in this new container.

4. In your main website GTM container, create a new tag that fires when a user accesses the page that contains the embedded iframe.

{: .box-note}
**Tag Configuration:**<br> Tag type: Custom HTML <br> Trigger: Page View, Page Path contains 2020-01-09-first-post <br> HTML code: See code box below <br>

```javascript
<script>
    (function(window) {
      createEvent(window, 'message', function(message) {
        try{
            var data = JSON.parse(message.data);
            var dataLayer = window.dataLayer || (window.dataLayer = []);
          	if(data && typeof data.event != 'undefined' && data.event.indexOf('iFrameButtonSubmitted') >= 0) {
          
              if (data.event) {
                  dataLayer.push({          
                      'event': data.event,
                      'postMessageData': data
                  });      
              } 
            }  
        } catch(e){
    		if({{Debug Mode}}) 
    		console.log(e);
  		};   
    });    
    
    // Event listener    
    function createEvent(eventListener, event, fx) {      
        if (eventListener.addEventListener) { 
          eventListener.addEventListener(event, fx);      
        } else if (eventListener.attachEvent) {  
           	eventListener.attachEvent('on' + event, function(event) {          
        		fx.call(eventListener, event);        
        	});      
        } else if (typeof eventListener['on' + event] === 'undefined' || eventListener['on' + event] === null) {    
            eventListener['on' + event] = function(evt) {          
        		fx.call(eventListener, event);        
        	};      
        }    
      }  
    })(window);
</script>"
```

Always verify the sender's identity. Any window can send a message to any other window, and you have no guarantees that an unknown sender will not send malicious messages. Having verified identity, however, you still should always verify the syntax of the received message. Otherwise, a security hole in the site you trusted to send only trusted messages could then open a cross-site scripting hole in your site.

**Pro's:**
* postMessage provides a safe means of communication between frames on different domains while still offering protection from cross-site scripting attacks.
* Current browsers fully support the postMessage method.
* Tracking snippets on the iframe simply send messages to the main website. No page hits are fired from within the iframe, so tracking control all sits with the main (parent) website. This takes care of the double page view tracking as well as campaign/referral attribution issues.

**Con's**
*  You need to have access to add some tracking snippets to the HTML code of the iframe.





## Solution 2: customTask











