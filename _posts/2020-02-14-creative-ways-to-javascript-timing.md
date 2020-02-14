---
layout: post
title: 4 Creative Ways to JavaScript Timing in Browsers
subtitle: Interesting alternatives for setTimeout, setInterval, and RAF
type: tech
opengraphImage: /images/postsImages/time.jpg
---

![Time]({{ site.baseurl }}/images/postsImages/time.jpg)
<cite>Photo by Lukas Blazek on Unsplash</cite>

This article assumes the availability for Web APIs, therefore, most methods suggested here don't work in NodeJs.

## Using an infinite synchronous loop in a Web Worker (not Service Worker)

Since Web Workers are essentially web threads, you can infinitely loop inside them without blocking the main thread. This gives you access to sub-millisecond time resolutions. This is especially good to make time critical decisions within the worker, and let the main thread know when (_accurately_) you see fit. Eg. Rerender something whenever microseconds are a prime number. To get access to microseconds, you can use [`performance.now`](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now).

#### Pros and cons

##### Pros

1.  Sub-millisecond resolution.
2.  Near zero cost on the UI thread.
3.  Fully asynchronous from a UI thread perspective. Thanks to Web Workers messaging design.
4.  Safe ending. Unlike `setInterval`, calling `worker.terminate` guarantees no more messages will be received. Quoting MDN, "The terminate() method of the Worker interface immediately terminates the Worker. This does not offer the worker an opportunity to finish its operations; it is simply stopped at once."

##### Cons

1.  Even though you can make sub-millisecond decisions, messaging back to the UI thread is asynchronous. You can't render as timely as you make the decision in the worker.
2.  Keeps a thread fully occupied. Might be heavy on phone batteries.
3.  Requires Web Worker support.
4.  Doesn't pause when the tab isn't focused.

#### Codesandbox example

<iframe
  src="https://codesandbox.io/embed/web-worker-timer-eiczf?fontsize=14&hidenavigation=1&theme=dark"
  style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
  title="Web Worker Timer"
  allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
  sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
></iframe>

## Using CSS animations for time events (particularly `animationiteration`)

<style>
  #wink .lid {
    animation: wink 5s infinite linear;
    transform: scaleY(0);
    transform-origin: 80% 40%;
  }
  @keyframes wink {
    18% {
      transform: scaleY(0);
    }
    20% {
      transform: scaleY(1);
    }
    22% {
      transform: scaleY(0);
    }
  }
</style>

<svg
  id="wink"
  version="1.1"
  width="200"
  xmlns="http://www.w3.org/2000/svg"
  x="0px"
  y="0px"
  viewBox="0 0 512.001 512.001"
  style="enable-background:new 0 0 512.001 512.001;"
  xml:space="preserve"
>
  <circle style="fill:#F7B239;" cx="256.005" cy="256.004" r="246.855" />
  >
  <path
    style="fill:#E09B2D;"
    d="M126.307,385.694c-88.801-88.802-95.798-228.426-20.998-325.241
    c-8.286,6.401-16.258,13.399-23.858,20.999c-96.401,96.401-96.401,252.698,0,349.099s252.698,96.401,349.099,0
    c7.599-7.599,14.597-15.573,20.999-23.858C354.733,481.492,215.109,474.495,126.307,385.694z"
  />
  <g>
    <circle style="fill:#F95428;" cx="100.572" cy="337.948" r="24.119" />
    <circle style="fill:#F95428;" cx="411.425" cy="337.948" r="24.119" />
  </g>
  <path
    d="M256,0C114.842,0,0.001,114.841,0.001,256.001S114.842,512.001,256,512.001c141.159,0,256.001-114.841,256.001-256.001
	S397.16,0,256,0z M256,493.701c-131.068,0-237.7-106.631-237.7-237.7S124.932,18.299,256,18.299
	c131.069,0,237.702,106.632,237.702,237.702S387.069,493.701,256,493.701z"
  />
  <path
    d="M363.356,207.453c-29.99,0-54.387,24.399-54.387,54.387s24.398,54.387,54.387,54.387s54.387-24.399,54.387-54.387
	S393.346,207.453,363.356,207.453z"
  />
  <path
    d="M148.647,207.453c-29.99,0-54.389,24.399-54.389,54.387s24.399,54.387,54.389,54.387s54.387-24.399,54.387-54.387
	S178.635,207.453,148.647,207.453z"
  />
  <path
    d="M312.911,318.973c-5.054,0-9.15,4.097-9.15,9.15c0,10.645-8.662,19.307-19.306,19.307s-19.306-8.662-19.306-19.307
	c0-5.053-4.095-9.15-9.15-9.15c-5.053,0-9.15,4.097-9.15,9.15c0,10.645-8.662,19.307-19.307,19.307s-19.307-8.662-19.307-19.307
	c0-5.053-4.097-9.15-9.15-9.15c-5.053,0-9.15,4.097-9.15,9.15c0,20.737,16.869,37.606,37.606,37.606
	c11.358,0,21.555-5.06,28.456-13.046c6.901,7.986,17.099,13.046,28.455,13.046c20.735,0,37.605-16.869,37.605-37.606
	C322.061,323.069,317.966,318.973,312.911,318.973z"
  />
  <g>
    <circle style="fill:#FFFFFF;" cx="182.406" cy="273.413" r="9.15" />
    <circle style="fill:#FFFFFF;" cx="400.775" cy="273.413" r="9.15" />
    <circle style="fill:#FFFFFF;" cx="127.094" cy="290.492" r="9.15" />
    <circle style="fill:#FFFFFF;" cx="345.463" cy="290.492" r="9.15" />
    <circle style="fill:#FFFFFF;" cx="378.414" cy="244.439" r="19.325" />
    <circle style="fill:#FFFFFF;" cx="163.704" cy="244.439" r="19.325" />
  </g>
  <path
    fill="#F7B239"
    class="lid"
    d="M363.356,207.453c-29.99,0-54.387,24.399-54.387,54.387s24.398,54.387,54.387,54.387s54.387-24.399,54.387-54.387
	S393.346,207.453,363.356,207.453z"
  ></path>

  <path
    fill="#F7B239"
    class="lid"
    d="M148.647,207.453c-29.99,0-54.389,24.399-54.389,54.387s24.399,54.387,54.389,54.387s54.387-24.399,54.387-54.387
	S178.635,207.453,148.647,207.453z"
  ></path>
</svg>

If you create a `div` (don't use a div though, see point 2 of cons) with an infinite animation. You can subscribe to its `animationiteration` event and get notified whenever `animation-duration` passes.

##### Pros

1.  Automatic suspension when the tab isn't in focus. The event doesn't fire at all when the tab isn't in focus. You don't need to worry about jammed calls that will all run at once when the tab is in focus again.
2.  Automatic clean up when the your hidden div is removed from the DOM. For example, if you have a React component that renders the time, you don't need to do anything on unmounting. The div will be removed and the event will not fire anymore.
3.  This is subjective but the subscription logic is beautiful. Eg. `.addEventListener("animationiteration", fun)`.
4.  Super clean way to delay the start of the timer by using `animation-delay`.

##### Cons

1.  A little too clever, might confuse your teammates/contributors.
2.  Depends on the DOM and CSSOM. Other CSS rules can interfere with yours. That's why I suggest to create an arbitrary non-existent tag like `<just-a-timer-element></<just-a-timer-element>`. Maybe create a custom element with the CSS animation code neatly tucked within? 🤯 (don't come after me, kthx).
3.  Doesn't work if the element has `display: none;`.
4.  Inaccurate. According to my testing, it can be off by a full `1ms`. You can try yourself in the example CSB below.

#### Codesandbox example

<iframe
     src="https://codesandbox.io/embed/css-animation-timer-fvssk?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="CSS Animation Timer"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>

### Using SVG <animate> tag (SMIL Animations)

<svg width="100" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg">
  <rect fill="orange" width="10" height="10">
    <animate attributeName="rx" values="0;5;0" delay="0" dur="5s" repeatCount="indefinite" />
  </rect>
</svg>

Consider

```html
<svg>
  <rect>
    <animate
      attributeName="rx"
      values="0;1"
      dur="1s"
      repeatCount="indefinite"
    />
  </rect>
</svg>
```

If you do `animate.addEventListener('repeat', fun)`, your function will be called every `dur` (1s) period.

##### Pros

1.  Works even when the SVG is `display: none;`.
2.  Stops automatically when the SVG is removed from the DOM.
3.  Doesn't begin until full page load.
4.  Auto-pauses when the tab isn't in focus.

##### Cons

1.  A little too clever, might confuse your teammates/contributors.
2.  Depends on the DOM and CSSOM. Same caveats as above. Other CSS rules can interfere with your configuration.
3.  Isn't supported in IE and Edge (before Chromium).
4.  Inaccurate. According to my testing, it can be off by a whopping 15ms. You can try yourself in the example CSB below.
5.  Doesn't begin until full page load. Yup, can be a bug and a feature.

#### Codesandbox example

<iframe
     src="https://codesandbox.io/embed/css-animation-timer-u8zun?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="SVG Animate as Timer"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>

## Using Web Animations API

[Web Animations API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Animations_API) allows you to animate DOM elements from within JavaScript.

Interestingly, you can animate unmounted elements! This gives you access to a timing mechanism in pure JS (and Web APIs).

Here is an alternative implementation of `setTimeout`

```js
function ownSetTimeout(callback, duration) {
  const div = document.createElement('div');

  const keyframes = new KeyframeEffect(div, [], { duration, iterations: 1 });

  const animation = new Animation(
    keyframes,
    document.timeline
  );

  animation.play();

  animation.addEventListener('finish', () => {
    callback();
  });
}
```

Neat, isn't it?

##### Pros

1.  Atomic. Doesn't need DOM interactions.
2.  Easy to understand by an unfamiliar person.
3.  Pauses when the tab isn't focused.

##### Cons
1.  Still a proposal. Don't use in production.
1.  Dire support situation. Probably only works in Chromium.
2.  Still a little counterintuitive.
3.  Pauses when the tab isn't focused. Can be bad if used as an alternative for `setTimeout`.
4.  Can't be used for intervals. Only `onfinish` event is available.
5.  Inaccurate. According to my testing, ±5ms can happen easily.

#### Codesandbox example

<iframe
     src="https://codesandbox.io/embed/svg-animate-as-timer-duv5q?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="Web Animations API as a Timer"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>

## This was fun

I realize this is a niche article. But I felt compelled to write it because I always thought `setTimeout` and `setInterval` were the only paths towards asynchronous delays and intervals. But turns out there are a few other options! And who knows, someone might have some strange constraints and might find this useful.
