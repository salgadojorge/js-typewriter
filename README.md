# Overview #

JS Typewriter simulates a user typing on a web page. Keystrokes are separated by a randomized, configurable delay that is sampled randomly. A caret may be displayed, and the caret can flash on and off. Callbacks can be used to trigger actions after each keystroke and/or trigger an action after a block of text has been displayed.

There is also a simple system for putting together more elaborate animations. See the examples below for more ideas.

# Example 1: Basic #

Getting started is very easy!

Instantiate a `Typewriter` on an element in your page. In this case, I'm using a paragraph with the ID "typewriter". `Typewriter` supports both jQuery objects (seen here) and plain old DOM objects. Next, tell the `Typewriter` what text to display.

```javascript
var typewriter = new Typewriter($("#typewriter"));
typewriter.typeText("Hello, world! This is a test... This is only a test.");
```

The complete source for [Example 1](./example1-basic/index.html) is included in this repository.

# Example 2: Terminal #

This example shows how to construct a more elaborate series of animations by simulating a terminal.

First, we will construct the `Typewriter` and configure a custom caret and custom timing. You can view this code in [example2-terminal/index.html](./example2-terminal/index.html).

```javascript
var typewriter = new Typewriter($("#terminal"));
typewriter.setCaret("_");
typewriter.setCaretPeriod(500);
typewriter.setDelay(100, 50);
animate(typewriter);
```

As in the previous example, we commence by constructing a new Typewriter and attaching it to a `<p>` with the ID "terminal". This page has some CSS to make it look something like an old-fashioned, VT-100 terminal, but the styling has no effect on the operation of the script.

Next we customize the caret. The caret will be an underscore character `_` instead of the default pipe character `|`. The caret will be alternately displayed for 500ms and then hidden for 500ms.

Then we set the keystroke delay to average 100ms between keystrokes with up to 50ms random variance. So between each keystroke, there will be a random delay between 50ms and 150ms.

The last line calls an animation function that is defined in [example2-termina/animate.js](./example2-terminal/animate.js). Let's take a look at that file.

```javascript
function animate(typewriter) {
    sequence = [
        {
            text: "trinity@localhost:~$ ",
            instant: true,
            delayAfter: 500
        },
        {
            text: "whoami\n",
            delayAfter: 500
        },
        {
            text: "trinity@the-matrix.net\ntrinity@localhost:~$ ",
            instant: true,
            delayAfter: 500
        },
        {
            text: "scp root@192.168.54.99:rickastley.mp4\b\b\b\b\b\b\b\b\b\b\b\b\b\bvirus.tgz .\n",
            delayAfter: 1000,
            callback: function () {console.log("Hehe... joke.")}
        },
        ...
    ];

    typewriter.playSequence(sequence);
```

This file declares a sequence of animation steps. Each step can display some text, run a callback after the text is displayed, and then delay a bit before running the next step.

For example, the first step displays some text that looks like a shell prompt. We set the `instant` boolean to true to indicate that we want this text to appear instantly, not to look like it is being typed by hand. (A shell prompt isn't usually typed by a person.) Then we set `delayAfter` to introduce a half second delay before the next animation step runs.

The second step simulates the user typing "whoami" and then a new line. There is another half second delay after this step.

The third step demonstrates the use of newline characters inside typed text. The newline character will be treated just like any other character, but please keep in mind that HTML user agents won't display a new line character unless you set the CSS accordingly! (Hint: `white-space: pre;`)

The fourth step demonstrates two interesting features. First, the text includes the `\b` escape code, which is the backspace character. If you include backspaces in your string, then `Typewriter` will simulate the user typing out a string and then backspacing over it. In this case, we have included just enough backspaces to delete the `rickastley.mp4` file name and then type something else in its place.

The other interesting feature in this step is the callback. Each step can have a callback triggered after it runs. Note that the callback runs _after_ the text finishes rendering but _before_ the delay. So in this case, the `Typewriter` would spend several seconds animating the text, write to the console immediately after the text animation, then wait another 1000 milliseconds before moving onto the next step.
