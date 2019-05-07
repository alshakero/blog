---
layout: post
title: How to Setup Your Web App Manifest Dynamically Using Javascript
description: From a URL or a JSON object
type: tech
opengraphImage: /images/postsImages/manifest.jpg
---
From a URL or a JSON object

![]({{ site.baseurl }}/images/postsImages/manifest.png)

For some reason or another, you might not be able to serve a json file that contains your [web app manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest). Maybe you’d like to construct the app manifest on the client side using custom client-selected theme color or icons without involving your server.

In such case, like with document.title, the first thing that comes to one’s mind it to dynamically inject this tag in your document’s head. Like:

```js
const link = document.createElement('link');
link.rel = 'manifest';
link.href = '/my-manifest.json';
document.head.appendChild(link);
```

**But this doesn’t work.**

## The trick

What I have found to be working, after the genius suggesion from [Marcin Warpechowski](undefined), is to have a link tag in the HTML with rel="manifest" but without href attribute. And use this tag later to populate your manifest. As in:

Your document:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>title</title>
    <link rel="manifest" id="my-manifest-placeholder">
  </head>
  <body>
    <!-- page content -->
  </body>
</html>
```

Now in Javascript you have two options:

1. Set href attribute using a URL:

```js
document.querySelector('#my-manifest-placeholder').setAttribute('href', '/my-dynamic-manifest-url.json');
```

2. Use a JSON object to set your manifest

```js
const myDynamicManifest = {
  "name": "Your Great Site",
  "short_name": "Site",
  "description": "Something dynamic",
  "start_url": "<your-url>",
  "background_color": "#000000",
  "theme_color": "#0f4a73",
  "icons": [{
    "src": "whatever.png",
    "sizes": "256x256",
    "type": "image/png"
  }]
}
const stringManifest = JSON.stringify(myDynamicManifest);
const blob = new Blob([stringManifest], {type: 'application/json'});
const manifestURL = URL.createObjectURL(blob);
document.querySelector('#my-manifest-placeholder').setAttribute('href', manifestURL);
```

Both ways work.

By this, you can allow your app users to customize the icon, colors, and even the very name of your app without any server involvement.
