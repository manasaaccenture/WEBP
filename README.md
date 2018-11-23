
### Introduction:
Through our apps, browsers and devices, we all use the **JPEG lossy format** to manage photos efficiently and the **PNG lossless format** to deliver graphics, icons and drawings.

In 2010 Google first introduced the new **WebP** (pronounced "Weppy") format, an open sourced **image format** that supports both **lossless and lossy** compression for images on the web. Using WebP, webmasters and web developers can create smaller, richer images that make the web faster.
WebP generates files significantly smaller than the popular JPEG and PNG formats. WebP also supports many advanced features, such as transparencies in its lossy mode, and more eye pleasing degradation in image quality on its lower quality, higher compression settings.
 
Smaller files mean less bandwidth consumption and reduced traffic costs for your online services and better user experience with faster delivery & image sharing times for users of your web sites and mobile apps.

It's important to remember, though, that WebP is **not a replacement** for JPEG and PNG images. It's a format you can serve to browsers that can use it, but you should keep older image formats on hand for other browsers.

### Converting Images to WebP with Node:

Node.js uses node package “imagemin” to convert your JPEGs and PNGs to WebP images. We are using Node Package Manager (npm) to install imagemin and the imagemin-webp plugin:

```
Cmd: 

npm install imagemin imagemin-webp.
```
create a new file named webp.js in web project's root folder. Type the script below into the file:
```
js:

var imagemin = require("imagemin"),   // The imagemin module.
webp = require("imagemin-webp"),  // imagemin's WebP plugin.
outputFolder = "./img",            // Output folder
PNGImages = "./img/*.png",         // PNG images
JPEGImages = "./img/*.jpg";        // JPEG images

imagemin([PNGImages], outputFolder, {
  plugins: [webp({
      lossless: true // Losslessly encode images
  })]
});

imagemin([JPEGImages], outputFolder, {
  plugins: [webp({
    quality: 65 // Quality setting from 0 to 100
  })]
});
```

The above script will process all JPEG and PNG images in the img folder and convert them to WebP. When converting PNG images, we set the lossless option to true. When converting JPEG images, we set the quality option to 65. We can experiment with even more settings at the [imagemin-webp plugin page](https://www.npmjs.com/package/imagemin-webp).

The script assumes that all of our JPEG and PNG images are in a folder named img. If this isn't the case, we can change the values of the PNGImages and JPEGImages variables. This script also assumes we want the WebP output to go into the img folder. If we don't want that, change the value of the outputFoldervariable to whatever we need. Once we are ready, run the script like so:
```
Cmd:
node webp.js
```
This will process all the images, and dump WebP counterparts into the img folder. 

### Using WebP in HTML

The below will work great, but only for browsers that support it. 
```
Html:

<img src="img/myAwesomeWebPImage.webp" alt="WebP rules." />
```
Some features just aren't going to work in every browser, and that's not going to change anytime soon. The easiest way we can make this work is to use the element to specify a set of fallbacks like so:
```
Html:

<picture>
  <source srcset="img/awesomeWebPImage.webp" type="image/webp">
  <source srcset="img/creakyOldJPEG.jpg" type="image/jpeg"> 
  <img src="img/creakyOldJPEG.jpg" alt="Alt Text!">
</picture>
```
This is probably our best for possible compatibility because it will work in every single browser, not just those that support the element. The reason for this is that browsers that don't support `<picture>` will just display whatever source is specified in the `<img>` tag.

### Using WebP Images in CSS 

The picture gets more complex when we need to use WebP images in CSS. Unlike the element in HTML which falls back gracefully to the `<img>` element in all browsers, CSS doesn't provide a built-in solution for fallback images that's optimal. Solutions such as multiple backgrounds end up downloading both resources in some cases.

[Modernizr](https://www.npmjs.com/package/imagemin-webp) is a well-known feature detection library that detects available features in browsers. WebP support just so happens to be one of those detections. Even better, we can do a custom Modernizr build with only WebP detection at [https://modernizr.com/download](https://modernizr.com/download), which allows us to detect WebP support with very low overhead.

When we add this custom build to our website via the `<script>` tag, it will automatically add one of two classes to the `<html>` element:
1.	The webp class is added when the browser supports WebP.
2.	The no-webp class is added when the browser doesn't support WebP.

With these classes, we will be able to use CSS to load background images according to a browser's capability by targeting the class on the tag:
```
Css:

.no-webp .elementWithBackgroundImage {
  background-image: url("image.jpg");
}

.webp .elementWithBackgroundImage{
  background-image: url("image.webp");
}
```

### Users with JavaScript Disabled
 
If we are depending on Modernizr, we must think about those users who have JavaScript disabled. If we are going to use feature detection that can leave some of our users in the dark, we need to test with JavaScript disabled. With the feature detection classes used above, JavaScript-less browsers won't even show a background image. This is because the disabled script never gets to add the detection classes to the `<html>` element.

To get around this, we'll start by adding a class of `no-js` to the tag:
```
Html:
<html class="no-js">
```

We'll then write a small piece of inline script that we'll place before any or tags:
```
Html:
<script>
  document.documentElement.classList.remove("no-js");
</script>
```
This will remove the `no-js` class on the `<html>` element when parsed.

When JavaScript is disabled, this small script never runs, so the `no-js` class will stay on the element. This means we can add another rule to provide an image type that has the widest support:
```
Css:
.no-js .elementWithBackgroundImage {
  background-image: url("image.jpg");
}
```
Now that we've done all of this, these are the use cases we can expect:
1.	Those who can use WebP will get WebP.
2.	Those who can't use WebP will get PNG or JPEG images.
3.	Those with JavaScript turned off will get PNG or JPEG images.


