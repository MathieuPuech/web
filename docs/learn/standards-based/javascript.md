---
title: JavaScript
eleventyNavigation:
  key: JavaScript
  parent: Standards-Based
  order: 40
---

JavaScript is a scripting language for the web. It was invented in 1993 by Brendan Eich at Mozilla (the makers of the Firefox browser). The language standard is maintained by [ECMA International](http://www.ecma-international.org/), a technology standards organization, so you'll sometimes see the terms "JavaScript" (a trademark of Oracle Corporation) or <abbr>JS</abbr> and "ECMAScript" or <abbr>ES</abbr> used interchangeably. You can use JavaScript to dynamically manipulate the HTML document.

The purpose of this document isn't to be a comprehensive introduction to JavaScript, rather, to introduce some programming patterns using modern JavaScript features that we've found to be useful when writing standards-based web applications. Check out [MDN's JavaScript documentation](https://developer.mozilla.org/en-US/docs/Learn/JavaScript) for a refresher before continuing.

## Referencing Reusable Assets with `import.meta.url`

When publishing reusable [modules](../es-modules), you may want to include assets like images and CSS. Consider the following structure:

```
root
├── modules
│   ├── module.js
│   └── asset.webp
└── index.html
```

```js
const imgSrc = './asset.webp';
const image = document.createElement('img');
image.src = imgSrc;
document.body.appendChild(image);
```

if `index.html` loaded `modules/module.js`, the image could not display, because the browser would request `/asset.webp` instead of `/modules/asset.webp`, as the author intended.

As of this writing, the es-module standard does not yet have a way of [importing non-javascript assets](https://github.com/tc39/proposal-import-assertions). If you tried to load assets using relative URLs, they would load relative to the _document_ rather than the _module_ path.

However, you can still publish modules that load bundled resources at runtime by using [`import.meta`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import.meta), a special object provided by the runtime which contains information about the current module.

```js
const imgSrc = new URL('./asset.webp', import.meta.url);
const image = document.createElement('img');
image.src = imgSrc.href;
document.body.appendChild(image);
```

This works by using the optional `base` parameter to the `URL` constructor, which in this case functions similarly to nodejs `path.resolve`.

## Composing Classes with JavaScript Mixins

> A mixin is an abstract subclass; i.e. a subclass definition that may be applied to different superclasses to create a related family of modified classes.
>
> - Gilad Bracha and William Cook, [Mixin-based Inheritance](http://www.bracha.org/oopsla90.pdf)

Some [object-oriented](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS) programming languages allow for "multiple inheritance", or the notion that a class can simultaneously inherit from two or more other classes. A "Baker" class might inherit simultaneously from the "Cook" and "CarbLover" classes. JavaScript does not allow multiple inheritances in `class` syntax. All JavaScript classes that extend other classes may only have one ancestor.

This presents a problem when you want to share some behaviour among classes which already inherit from a base class.

Let's take for example Logging. Imagine you have 3 Pages

```
                                +-----------+
                          +---->+  PageRed  |
                          |     +-----------+
                          |
+------------+   +------+ |     +-----------+
| AppElement +-->+ Page +------>+ PageGreen |
+------------+   +------+ |     +-----------+
                          |
                          |     +-----------+
                          +---->+  PageBlue |
                                +-----------+
```

```js
// Assume AppElement is a base class for all elements of the app
class Page extends AppElement {
  /*...*/
}

class PageRed extends Page {
  static color = 'red';
}

class PageGreen extends Page {
  static color = 'green';
}

class PageBlue extends Page {
  static color = 'blue';
}
```

### Single Inheritance

Say we wanted to log the page color whenever someone enters the red page. Using JavaScript's single inheritance, we could define a `LoggedPageRed` class that inherits from `PageRed`.

```
                                +-----------+     +---------------+
                          +---->+  PageRed  +---->+ LoggedPageRed |
                          |     +-----------+     +---------------+
                          |
+------------+   +------+ |     +-----------+
| AppElement +-->+ Page +------>+ PageGreen |
+------------+   +------+ |     +-----------+
                          |
                          |     +-----------+
                          +---->+  PageBlue |
                                +-----------+
```

```js
class LoggedPageRed extends PageRed {
  connectedCallback() {
    super.connectedCallback?.();
    console.log(this.constructor.color);
  }
}
```

This works fine for the red page, but what if we needed implement logging for _both_ the red and green pages, but _not_ the blue page. There's nothing about the logging code which is specific to the red instance of `Page`. How could be generalize the logging behaviour so as to apply it to any kind of class?

- We can't put the logic in `Page` as blue inherits from `Page`, and it shouldn't log
- We can't reuse the logic from `LoggedPageRed` in some new `LoggedPageGreen` class, as we can not extend from multiple sources.
- We could implement a new intermediary base class `PageThatLogs`, but down the line we might want to separate "logging" behaviour" from "pageness".

We need a way to define "A thing that logs" which is separate from "A thing that is a Page"

```
                                +---------------------+
                          +---->+  PageRed that logs  |
                          |     +---------------------+
                          |
+------------+   +------+ |     +---------------------+
| AppElement +-->+ Page +------>+ PageGreen that logs |
+-----+------+   +------+ |     +---------------------+
      |                   |
      |                   |     +-----------+
      |                   +---->+  PageBlue |
      |                         +-----------+
      |
      |                         +---------------------+
      +------------------------>+ Element that logs   |
                                +---------------------+
```

### Class Mixins

JS class mixins let us share behaviours using class syntax. At it's heart, class mixins are just function composition. JS classes are functions, and class mixins are higher order functions on classes.

You define a class mixin like you would any other function.

```js
const LoggingMixin = superclass =>
  class extends superclass {
    connectedCallback() {
      super.connectedCallback?.();
      console.log(this.constructor.color);
    }
  };
```

And you apply it like you would any other function

```js
class PageRed extends LoggingMixin(Page) {}
class PageGreen extends LoggingMixin(Page) {}
class PageBlue extends Page {}
```

You can use multiple mixins as well - function composition!

```js
class PageRed extends LoggingMixin(PageRouterMixin(StateStoreMixin(Page))) {
  /*...*/
}
```

With that approach we can extract logic into a separate code piece we can use where needed.

### Read More

For a more in-depth technical explanation please read [Real Mixins with JavaScript Classes](https://justinfagnani.com/2015/12/21/real-mixins-with-javascript-classes/).

You can see the full blog post [Using JavaScript Mixins The Good Parts](https://dev.to/open-wc/using-javascript-mixins-the-good-parts-4l60).
