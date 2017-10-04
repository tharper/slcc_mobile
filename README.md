# slcc_mobile
1. Copy the entire code from the article.htmlfile and paste it into a new file.
2. Save the new file as article.amp.html.

Our AMP version of the article is just a copy of the original article right now. Let's convert it to an AMP.

To begin, we will add the AMP library file. This alone won't make your new file a valid AMP page, but we'll see below how the AMP library can help us figure out what we need to do to fix that.

To include the AMP library, add this line to the bottom of the \<head> tag:

````
<script async src="https://cdn.ampproject.org/v0.js"></script>
````

Load the new article.amp.html page in your browser and then, open the Developer Console in Chrome (or your preferred browser).

When you inspect the JavaScript output in the Developer Console (make sure you have the Console tab selected), you should see this log entry:

The AMP library includes an AMP validator that will tell you if there is anything that is keeping your page from being a valid AMP document. Enable the AMP validator by adding this fragment identifier to your document URL:
````
#development=1
````
For example:
````
http://localhost:63342/slcc_mobile/article.amp.html#development=1
````
In the Developer Console, you should receive several validation errors (you may need to manually refresh the page in your browser to see these):

In order to make this a valid AMP document we're going to have to fix all of these errors--which is exactly what we'll be doing in this codelab.

Before we do that, let's simulate a mobile device experience in the browser's developer tools since we are working with a mobile news article. For example, in Chrome DevTools, click the mobile phone icon, and select a mobile device from the menu.

You should see a mobile simulated resolution in your browser.

Now we're ready to get to work! Let's step through the validation errors one by one and address how they relate to AMP.

Note that the errors may appear in a different order in your console.


### Include charset
We will begin by fixing the following error:
````
The mandatory tag 'meta charset=utf-8' is missing or incorrect.
````
To correctly display text, AMP requires that you specify the charset for the page. The meta charset information must also be the first child of the \<head> tag. The reason this tag must be first is to avoid re-interpreting content that was added before the meta charset tag.

Add the following code as the first line of the \<head> tag:
````
<meta charset="utf-8" />
````
Save the file and reload the page. Verify that the charset error no longer appears.

### Include canonical link
Now, let's look at the following error:
````
The mandatory tag 'link rel=canonical' is missing or incorrect.
````
Every AMP document needs to have a link referencing the "canonical" version of that document. We'll learn more about what canonical pages are and different approaches to canonical linking in the Making your page discoverable step of this tutorial.

For this tutorial we'll consider the original HTML article that we're converting to be the canonical page.

Go ahead and add the following code below the \<meta charset="utf-8" /> tag:
````
<link rel="canonical" href="/article.html">
````
NOTE — You can create a standalone canonical AMP page. The canonical link is still required, but should point to the AMP article itself:
````
<link rel="canonical" href="article.amp.html">
````
Now, reload the page. Although there are still plenty of errors to fix, the canonical link error is no longer present.

### Specify the AMP attribute
AMP requires an attribute on the root \<html> element of a page to declare the page as an AMP document.
````
The mandatory attribute '⚡' is missing in tag 'html ⚡ for top-level html'

The mandatory tag 'html ⚡ for top-level html' is missing or incorrect.
````
The above errors can be resolved by simply adding the ⚡attribute to the \<html> tag like so:

````
<html ⚡ lang="en">
````
Now, go ahead, reload the page and check that both errors are gone.

NOTE — Although specifying the ⚡ is the recommended approach, it's also possible to use the amp attribute in place of the ⚡ attribute, like so:
````
<html amp lang="en">
````
### Specify a viewport
Next, let's address the following error:
````
The mandatory tag 'meta name=viewport' is missing or incorrect.
````
AMP requires the definition of a width and minimum-scale for the viewport. These values must be defined as device-width and 1, respectively. The viewport is a common tag included in the \<head> of an HTML page.

To resolve the viewport error, add the following HTML snippet to the \<head> tag:
````
<meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">
````
The values specified for width and minimum-scale are the required values in AMP. Defining initial-scale is not mandatory but it’s a commonly included in mobile web development and it's recommended. 

As before, reload the page and check if the error has disappeared.

### Replace external stylesheets
The following error is related to our use of stylesheets:
````
The attribute 'href' in tag 'link rel=stylesheet for fonts' is set to the invalid value 'base.css'.
````
Specifically, this error is complaining about the following stylesheet link tag in our \<head> tag:
````
<link href="base.css" rel="stylesheet" />
````
The problem is that this is an external stylesheet reference. In AMP, to keep the load times of documents as fast as possible, you cannot include external stylesheets. Instead, all stylesheet rules must be added inline in the AMP document using \<style amp-custom></style> tags.

````
<style amp-custom>

/* The content from base.css */

</style>
````
So, let's resolve the error:

1. Remove the \<link> tag pointing to the stylesheet in the \<head> and replace it with an inline \<style amp-custom>\</style> tag. The amp-custom attribute on the style tag is mandatory.
2. Copy all the styles from the base.css file into the \<style amp-custom>\</style> tags.

Once again, reload the page and verify that the stylesheets error has disappeared.

NOTE — Not only is inline styling required but there is a file size limit of 50 kilobytes for all styling information. You should use CSS preprocessors such as SASS to minify your CSS before inlining the CSS in your AMP pages.

IMPORTANT — You can only have one style tag in your entire AMP document. If you have several external stylesheets referenced by your AMP pages, you will need to collate these stylesheets into a single set of rules.

### Exclude third-party JavaScript
While stylesheets can be reworked relatively easily with AMP by inlining the CSS, the same is not true for JavaScript.
````
The tag 'script' is disallowed except in specific forms.
````
In general, scripts in AMP are only allowed if they follow two major requirements:

1. All JavaScript must be asynchronous (i.e., include the async attribute in the script tag).
2. The JavaScript is for the AMP library and for any AMP components on the page.

This effectively rules out the use of all user-generated/third-party JavaScript in AMP except as noted below.

NOTE — The only exceptions to the restriction on user-generated/third-party scripts are:
1. Script that adds metadata to the page or that configures AMP components. These will have the type attribute application/ld+json or application/json.
2. Script included in iframes. Including JavaScript in an iframe should be considered a measure of last resort. Wherever possible, JavaScript functionality should be replaced by using AMP components. We will explore our first AMP component in the next section.
Try opening the external base.js file. What do you see? The file should be empty of any JavaScript code and only include a comment of information such as this:
````
/*

This external JavaScript file is intentionally empty.

Its purpose is merely to demonstrate the AMP validation error related to the
use of external JavaScript files.

*/
````
Considering that this external JavaScript file is not a functional component of our website, we can safely remove the reference entirely.

Remove the following external JavaScript reference from your document:
````
<script type="text/javascript" src="base.js"></script>
````
Now, reload the page and verify that the script error has disappeared.

### Include AMP CSS boilerplate
The following errors reference missing boilerplate code:
````
The mandatory tag 'noscript enclosure for boilerplate' is missing or incorrect.
The mandatory tag 'head > style : boilerplate' is missing or incorrect.
The mandatory tag 'noscript > style : boilerplate' is missing or incorrect.
````
Every AMP document requires the following AMP boilerplate code:
````
<style amp-boilerplate>body{-webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both;-ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both;animation:-amp-start 8s steps(1,end) 0s 1 normal both}@-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}@keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}</style><noscript><style amp-boilerplate>body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none}</style></noscript>
````
Add the boilerplate code to the bottom of the \<head> tag of your document.

The \<style amp-boilerplate> tag initially hides the content of the body until the AMP JavaScript library is loaded, then the content is rendered. AMP does this to prevent unstyled content from rendering, also known as Flash Of Unstyled Content (FOUC). This helps ensure that the user experience feels truly instant as the page’s content appears all at once and everything above the fold is rendered together. The second tag reverts this logic if JavaScript is disabled in the browser.

### Replace <img> with <amp-img>
AMP doesn't support the default HTML counterparts to displaying media, which explains the following error:
````
The tag 'img' may only appear as a descendant of tag 'noscript'. Did you mean 'amp-img'?
````
AMP has a web component specifically designed to replace the \<img> tag, it's the <amp-img> tag:
````
<amp-img src="mountains.jpg"></amp-img>
````
Replace the \<img> tag with the above <amp-img> tag and run the validator again. You should receive several new errors:
````
Layout not supported: container
The implied layout 'CONTAINER' is not supported by tag 'amp-img'.
````
Why did amp-img trigger another error? Because amp-img is not a direct substitute of the traditional HTML img tag. There are additional requirements when using amp-img.

### AMP layout system
The layout error is telling us that amp-img does not support the container layout type. One of the most important concepts in AMP’s design is its focus on reducing the amount of DOM reflow required to render its web pages.

To reduce DOM reflow, AMP includes a layout system to ensure the layout of the page is known as early as possible in the lifecycle of downloading and rendering the page.

The image below compares how an HTML page if often laid out compared to the approach AMP enforces. Notice in the approach on the left how the text reflows each time an ad or image is loaded. AMP's approach to layout keeps the text from moving around--even if the images and ads take a long time to load.

![alt text](https://github.com/tharper/slcc_mobile/raw/master/tut-convert-html-layout-system.png)

The AMP layout system allows for elements on a page to be positioned and scaled in a variety of ways -- fixed dimensions, responsive design, fixed height and more.

The layout system inferred the layout type for the amp-img as the container type. However, the container type is only applicable to elements that contain children elements. The container type is incompatible with the amp-img tag, which is the reason for this error.

Why was the container type inferred? Because we did not specify a height attribute for the amp-img tag. In HTML, reflow can be reduced by always specifying a fixed width and height for elements on a page. In AMP, you need to define the width and height for amp-img elements so that AMP can pre-determine the aspect ratio of the element.

Add the width and height to your <amg-img> tag as follows:
````
<amp-img src="mountains.jpg" width="266" height="150"></amp-img>
````
Refresh the page and check the validator; you should no longer see any errors!

You now have a valid AMP document, but the image doesn’t look so great because it is awkwardly positioned on the page. By default when you specify the height and width for an amp-img AMP will fix the dimensions to what you specify--but wouldn't it be great if AMP would scale the image to responsively stretch and fit the page no matter the screen size?

Fortunately AMP can figure out the aspect ratio of elements from the width & height you specify. This allows the AMP layout system to position and scale the element in a variety of ways. The layout attribute informs AMP of how you want the element positioned and scaled.

Let's set the layout attribute to responsive so that our image scales and resizes:
````
<amp-img src="mountains.jpg" layout="responsive" width="266" height="150"></amp-img>
````
Voila! Our image is in the correct aspect ratio and responsively fills the width of the screen.

Refresh the page and look at the console output. You should be greeted with the following message:
````
AMP validation successful.
````
### Navigate with a sidebar
A common navigation technique is to add a menu icon that when clicked reveals a set of navigation links (from the side of the page). In AMP, we can create such navigation with the amp-sidebar component.

First, we must add the amp-sidebar component’s JavaScript to the \<head> tag:
````
<script async custom-element="amp-sidebar" src="https://cdn.ampproject.org/v0/amp-sidebar-0.1.js"></script>
````
Next, we want to display a menu icon. When the icon is tapped, it will open the sidebar. Replace the \<header> with the following code to display a "hamburger" icon instead of a home icon:
````
<header class="headerbar">
  <div role="button" on="tap:sidebar1.toggle" tabindex="0" class="hamburger">☰</div>
  <div class="site-name">News Site</div>
</header>
````
In the above code, we toggle the sidebar through the on action attribute on the amp-sidebar element, which is identified by the sidebar1 ID. Let's add the sidebar.

Add the following HTML just after the \</header>:

````
<amp-sidebar id="sidebar1" layout="nodisplay" side="left">
  <div role="button" aria-label="close sidebar" on="tap:sidebar1.toggle" tabindex="0" class="close-sidebar">✕</div>
  <ul class="sidebar">
    <li><a href="#">Example 1</a></li>
    <li><a href="#">Example 2</a></li>
    <li><a href="#">Example 3</a></li>
  </ul>
</amp-sidebar>
````
Our sidebar will be hidden, but when the user taps the hamburger icon, the menu will appear from the left side of the screen. To close the menu, the user can tap the X icon.

Finally, add these style rules to your inline CSS:

````
.hamburger {
  padding-left: 10px;
}      
.sidebar {
  padding: 10px;
  margin: 0;
}
.sidebar > li {
  list-style: none;
  margin-bottom:10px;
}
.sidebar a {
  text-decoration: none;
}
.close-sidebar {
  font-size: 1.5em;
  padding-left: 5px;
}
````

Finish your project by committing your code and creating a pull request