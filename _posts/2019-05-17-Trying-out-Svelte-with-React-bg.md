---
layout: post
title: Trying out Svelte as a React Developer
subtitle: By building a small flappy bird game
type: tech
opengraphImage: /images/postsImages/svelte.png
---
![Svelte]({{ site.baseurl }}/images/postsImages/svelte.png)

I have been so excited to try out Svelte v3 since it came out in late April. But I wasn't sure what to build. I built TodoMVC app and it didn't feel enough to test out a framework, took me around 30 mins. Plus, [it's already built](https://github.com/sveltejs/svelte-todomvc) by Svelte team and this made it feel less insightful for some reason. 

I ended up building a small two-hours buggy version of Flappy Bird that you can play [here](https://alshakero.github.io/svelte-hello/) (use spacebar to jump). Or view the source [here](https://github.com/alshakero/svelte-hello). The bundle size of this game is 5KB gzipped ⚡.

## Disclaimer
I realize this is a small app and it probably does not represent decent team-built apps. It is meant to give me a rough idea about Svelte. And I'm sharing this rough idea with you. No conclusions made here. But feel free to make your own 😁

## Why Flappy Bird?
Because it's the simplest app that I could think of that needs a nice cocktail of **State management**, **DOM access**, **CSS animations**, **Event handling**, **Control flow** and **SVG**. 

1. **State management**: it needs across component two-way data-binding. As the diagram below shows. I divided the app into four components. 
![State]({{ site.baseurl }}/images/postsImages/SvelteState.png)
The components are: 
- `App`: the centric component where the game logic exists. This component handles key presses, moves the `Bird` component (passes new X and Y), increments the score, and determines if the game is over. 
- `Bird`: this component accepts X and Y coordinates and renders the bird at those coordinates.
- `World`: the animated pipes. Since the bird is always at the mid-point of the X-Axis, I only needed to worry about the Ys to determine whether the bird collided with the pipes. This component animates the pipes and upsends the collision points (top-Y and the bottom-Y) to `App` component.
- `Score`: Just renders the score at top right corner.

2. **DOM access**: It plays some audio loop using an `<audio />` HTML5 tag. And I had to call `player.pause()` when the game is over. I needed to grab the reference to this element to do that.

3. **CSS animations**: the easiest way to implement the flappy wings.

4. **Event handling**: To handle key up and key down.

5. **Control flow**: A simple if-else for game-over state.

6. **SVG**: the whole game renders in an SVG.

## How was it?

**Amazing. Easy 8/10**.

10 being an ideal standard that doesn't exist yet. The most seemless framework experience I have had in a long time. And I tried AngularJS, Angular, Polymer, Vue.js, and have been writing React apps for over two years. 

The unique thing about Svelte is that it tries its best to get out of the way. It's there, but you mostly notice its existence when it is trying to help you. I didn't feel I was building a Svelte app the way you feel when you build a React app. I was just building an app. Let me explain.

#### I didn't have to think about state management
What is even state management? Svelte makes it feel like an ancient problem. You write JavaScript and use your variables (read and write) around the component. If you need to use some variable across components you do `<Component bind:name={myNameVar}>`. Then in your child component you use `name` like a good ol' JS variable and the parent component will re-render when needed.

This `bind` keyword is the first time I needed to consult the docs. The second thing that I needed to hit Google for is array mutation. It turns out that Svelte doesn't handle array mutations or any type of mutations such as `arr.push(newItem)`. I ended up doing `arr = [newItem, ...arr]`, it was a minor bummer that the framework didn't take care of it. Since it took care of all other state updates.

I love React. And I love hooks; I have been writing custom hooks since hooks came around. That said, Svelte makes you **feel** (as opposed to think) that hooks are a solution to a problem that is only created by React. I stress the word **feel** for a reason; of course the problem isn't created by React, invalidation is a real problem, but Svelte takes care of it all and you start to feel that every hook you'd used or written is a waste of time. At least that's my experience.

For instance:
```js
const [value, setValue] = useState(0);
...
setValue(5);
```
... is clean and lovely. 

But can it really be cleaner than:
```js
const value = 0;
...
value = 5;
```

Especially when you need to pass `value` to a child component. In React, you'll have to find a way to notify the parent about an update to `value` on your own. In Svelte `<ChildComponent bind:value={value}>` would do.

#### Async calls like `setTimeout`, `setInterval` and `requestAnimationFrame` are easy
Quite close to when you write a vanilla JS app, some cleanup might still be needed after you're done with those. But it is certainly more straightforward than using them with hooks. A few people found these cases confusing with React hooks, including myself. See [this](https://github.com/facebook/react/issues/14195) and [this](https://overreacted.io/making-setinterval-declarative-with-react-hooks/). In Svelte, they're as confusing as they're in vanilla JS.

#### No need for CSS modules or any external scoping mechanisms
Similar to Vue.js, most credit goes to the Single File Components model. You just write your CSS next to your HTML and JS. This might result in a lot of repeated CSS across the app since you can't import external CSS. But is that a bug or a feature? You won't search for the rule that is affecting your styles anymore, it's for sure next to the element it's stylizing.

#### Window events are wholesome
In this game, I needed to add event listeners to `window`. This would indeed be neat in React, hooks or otherwise, but can it be this clean?

```html
<svelte:window on:keypress={handleKeypress} on:keyup={handleKeyup} />
```
And Svelte will remove those listeners upon unmounting automatically. Less code, less problems.

#### String interpolation is lovely
Not much different from React, but worth a mention. Check this `viewBox` attribute of the game's SVG 
```js
viewBox="0 0 {width} {height}"
````
Not a big deal. But I like it more than:
```js
viewBox={`0 0 ${width} ${height}`}
```
The VS Code Svelte linter detects your mistakes if you do `value="Hello {somethingUndefined}"`.

#### Shorthands are nice
```html
<!-- These are equivalent -->
<input bind:value={value}>
<input bind:value>
```

```html
<!-- These are equivalent -->
<div class="{active ? 'active' : ''}">...</div>
<div class:active={active}>...</div>

<!-- Shorthand, for when name and value match -->
<div class:active>...</div>
```

##### Mutliple class toggles are 🔥
```html
<!-- Multiple class toggles can be included -->
<div class:active class:inactive={!active} class:isAdmin>...</div>
```

These are the shorthands I used. I think there is a few more shorthands.

### Some criticism 
1. I didn't like using `degit`. I was there to try out Svelte, but I was also introduced to `degit`. It's a straightforward tool but it was a little extra cognitive load that made my entrance a tad less exciting. 

2. Only one `<script>` element is allowed per component. At one moment I wanted to have two to isolate imports from game logic. That didn't work.

3. The quick start template is too simplistic and ugly. I wish there were a tool like `create-react-app`. A purple Hello World was a bit off-putting.

4. The VS Code support is somewhat buggy. In this example the linter complaining about an unused import, even though it is indeed used. 
![The linter complaining about an unused import, even though it is indeed used]({{ site.baseurl }}/images/postsImages/svelte-linter.jpg)
Sometimes it also showed me TS errors:
![The linter complaining about an unused import, even though it is indeed used]({{ site.baseurl }}/images/postsImages/svelte-linter-2.jpg)

5. GitHub doesn't recognize `.svelte` format, at least not yet. The code renders as plain text 😞. 

### The non-conclusion
It's hard to judge at a such early stage. I can say that for me, in the next few months, choosing between React and Svelte will be a question worth discussing before starting every new project. React is established and has a great community and most of my frontend experience is using it. Svelte is clean, straightforward, developer friendly, and eh-em, 5KB bundle.




























