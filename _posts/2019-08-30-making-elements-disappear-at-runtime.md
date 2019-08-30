---
layout: post
title: "Making Elements Disappear At Runtime"
---
Sometimes we find ourselves needing to make changes at runtime. This is something I've been encountering more as I get into automation via puppeteer, albeit it's not a common development task for me.

The importance of the problem in context of what I'm trying to achieve is somewhat trivial, but it's a trivial issue I hadn't encountered until recently. I found it interesting and I'm sure there are better ways to do it other than the two methods I will go over.

### Automating user flows via puppeteer 

It's that simple. Puppeteer provides an API that allows you to control a chrome/chromium browser instance by way of the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/). 

### So what's the problem?

We've created a component to assist the user in completing the flows with autofill capabilities. This specific project runs in a "test environment" in which there are no actual API calls being made to the server, but rather we've created a test utils library to help carry out any actions possible on our live sites.

This little component is present throughout the flow and was built with an unobtrusive minimized state, but it is still visible on the page. It's not a problem in the context of actual users who want play with the application but still have the option to pull up the assistant and get help.

But the automation tasks do not require this, nor do we want to show it when programmatically taking screenshots and/or videos.

### Making the change at run time

We want to build the application with the assistant, but have the option to not show it at runtime in context of the automation script.

My initial approach was to use puppeteers evaluate method to inject the rule on the initial load of the page by inserting it into an arbitrary stylesheet:

```JavaScript
    await page.evaluate(() => {
        const sheet = document.styleSheets[0];
        sheet.insertRule('.myComponent {display: none}');
    });
```

The second approach was to use the same evaluate method, but to inject the rule's value by way of the HTML Elements styles property:

```JavaScript
    await page.evaluate(() => {
        const el = document.querySelector('.myComponent');
        el.style.display = "none";
    });
```            


Both approaches landed me with the same result. But let's unpack the approaches a little to see how they differ:

```JavaScript 
page.evaluate(pageFunction: <function|string>, ...args): <Promise>
```

`page.evaluate()` takes a callback with optional parameters, allowing the callback to execute in context of the page itself.

So this is useful for our use case.

Now, for the first approach we are grabbing the list of style sheets currently available on the page. This has a caveat, being that we're basically grabbing an arbitrary stylesheet which may or may not be related to what we're seeing on the page. For example, it may be for print related styles. 

We could add an additional check by looking at the style sheet's media list and checking that the type returns `all` and not `print`, `handheld`, `projection`, etc.

```JavaScript
    if (sheet.media.mediaText === 'all')
```

After grabbing our style sheet we simply insert the rule we wish to see take effect on the page. Internally, this is being inserted into `CSSStyleSheet.cssRules` which is the internal rule list of the style sheet. 
In our case we're setting the display property of the root element of the component to none. The component is still rendered, it's just simply not displayed on the page. 

Now, taking a look at the second method we are grabbing the HTML Element of the component it's self via the DOM API's document.querySelector and targeting it by class name. Once we have our element, we simply get it's associated styles and target it's display property, setting our desired value.

Both these methods are dirty in my opinion, but for my specific use case it's acceptable being that it's for internal user. I ended going with the second approach beasue it felt somewhat more specific and therefore more controlled.

I hope to come back in the future and follow-up with a post outlining a better solution.
