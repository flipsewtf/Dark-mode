# Dark mode guide for Tumblr themes
This guide is (largely) aimed at theme-makers, because we're going to be using [CSS Variables](https://www.w3schools.com/css/css3_variables.asp) — if your theme does not support these, you will have to do a certain amount of editing - but only once. So fear not, in the end you'll cut considerable time off of creating themes in the future.
> [!NOTE]
> If you know what you're doing by all means skip ahead and copy-paste. I just want to explain what the different lines of codes.

## Overview
### What we want
- When selected, dark theme to stay dark when refreshing a page.
- To prevent default (light) theme from flashing before the displaying dark mode when browsing (for accessibility).
- If a visitor has dark mode on their PC/device, honour their preference and display dark mode with the option to turn on light mode (for accessibility).
- a snappy transition (also for accessibility - but, I will show you a way to override it like I have on my main toggle button in the top right corner).

### What we're going to do

- Use a data-theme attribute on the `<html>` node, instead of depending on `<body>`.
- Which means we can run the JS before the DOM finish rendering
- Run local storage-check in `<head>`
- Use CSS variables for colours, etc.
- Use `class=""` instead of `ID=""` if we have multiple toggle-buttons, especially for responsive design.

## Let's begin

We're going to be creating the toggle button in the top left corner of the page, displaying a sun and a moon.

First, we need to add data-theme to our`<html>` tag. "Light" will be our default theme:

```html
<!DOCTYPE html>
<html lang="en" data-theme="light">
```

## JavaScript

This is pure JS so we don't need to worry about jQuery. Place this right before `</body>` within a `<script>` tag.

Two things are happening here: When we toggle the button the theme changes, but right before it does so, it adds the class `"theme-transition"` to the `<html>` tag where we remove all transition you might have added to links, divs, etc.

This gives it a snappy transition of the theme, so we don't end up with multiple timeframes of transitions that makes it look sloppy and potentially annoying to motion-sensitive people.

You can override this transition like I have on the toggle-button in the top right corner. I'll show you this later on.

```Javascript
const themeToggleButtons = document.querySelectorAll(".theme-toggle");

themeToggleButtons.forEach((btn) => {
    btn.addEventListener("click", function () {

        // Add theme-transition class for smooth transitions
        document.documentElement.classList.add("theme-transition");

        // Retrieve the current theme and determine the target theme
        let currentTheme = document.documentElement.getAttribute("data-theme");
        let targetTheme = (currentTheme === "light") ? "dark" : "light";

        // Remove theme-transition class after a short delay
        window.setTimeout(function () {
            document.documentElement.classList.remove("theme-transition");
        }, 50);

        // Set the data-theme attribute and store in local storage
        document.documentElement.setAttribute("data-theme", targetTheme);
        localStorage.setItem("theme", targetTheme);
    });
});
```

In the first line you have `"theme-toggle"`. This will be the `class=""` we will give the button to toggle the theme later on. Notice it's a class instead of an ID, so we can have several toggle buttons which becomes convenient in responsive design.

But it's not enough to save our preferences in `LocalStorage`, we need to retrieve it whenever we refresh a page or browse around a blog or revisit.

This piece of code is usually within the script above, but by placing this part in the `<head>` we avoid that annoying flash of default/light theme. (which can be harmful to light sensitive-people.)

Place this within a `<script>` tag, after all the main <meta> tags, but before Tumblr's own meta tags.

```Javascript
const storedTheme = localStorage.getItem("theme") || (window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light");
if (storedTheme) {
    document.documentElement.setAttribute("data-theme", storedTheme);
}
```

## CSS Variables
So what are CSS Variables? Well, instead of copy-pasting the same colours over and over again, you can place them in variables- wait, does that sound familiar? It should if you're a theme-maker. Tumblr's meta tags (variables) works just like a CSS variable. We put all variables in the `:root` selector, which is placed at the very top of the stylesheet. Let's have a look:

```` CSS
:root {
    --font-size: 14px;
    --post-width: 540px;
    --NameItWhateverYouWant: 0.4em;
    --c-text: white;
    --c-bg: black;
}
````
