---
title: Introducing: Modern Web.
published: false
description: Developer tools for modern web development
date: 2020-08-10
tags: [javascript, test, modern-web]
cover_image: /blog/introducing-modern-web/introducing-modern-web-blog-header.jpg
---

We are excited to introduce our brand new project: Modern Web.

## What is Modern Web?

A few years ago we started the [Open Web Components](https://open-wc.org/) project. Our goal was to help people develop web component, and we created guides and tools to help people do this. While working on this project, we realized that a lot of the things we were making were not necessarily specific to web components.

To maintain focus within the open-wc project, and to share our work with the larger developer community, we decided to split up the project and create Modern Web. Open-wc will gain a renewed focus for web component specific topics, while in Modern Web we will work on generic tools and guides for web component.

## The goal for Modern Web

Modern browsers are a powerful platform for building websites and applications. We should work with what's available in the browser first before reaching for custom solutions.

Because you're working with the browser rather than against it, code, skills, and knowledge remain relevant for a longer time. Development becomes faster and debugger easier because there are fewer layers of abstractions involved.

At the same time, we are not ignorant of the fact that not all problems can be solved elegantly by the browser today. We support developers making informed decisions about introducing tools and customizations to their projects, in such a way that developers can upgrade later as browser support improves.

## Our plans

This announcement marks the official "release" of Modern Web. Our website is available at [modern-web.dev](https://modern-web.dev), and our available packages are publishing on NPM within the [@web](https://www.npmjs.com/org/web) namespace. Our code is open source and on [github.com/modernweb-dev/web](https://github.com/modernweb-dev/web). For updates, you can follow us on [Twitter](https://twitter.com/modern_web_dev).

### Guides

Our website launches with a [Learn section](https://modern-web.dev/learn/standards-based/) that teaches modern and not so modern browsers features that help with development. We don't aim to duplicate content already available on other websites, we primarily cover features and concepts that often underused or misunderstood.

### Dev server

es-dev-server is the most popular package at open-wc. It is also the prime example of a tool that is not limited to web components alone. We've been working on its spiritual successor which we will call `@web/dev-server`.

The most important improvements are first-class supports for rollup plugins through [@web/dev-server-rollup](https://modern-web.dev/docs/dev-server/plugins/rollup/) as well as the ability to plug into esbuild loaders through [@web/dev-server-esbuild](https://modern-web.dev/docs/dev-server/plugins/esbuild/).

The dev server is not quite finished yet, but we've already built the basic parts to power our new test runner. We will finalize our work on the dev server very soon.

### Test Runner

We are very excited to announce today the official release candidate of [@web/test-runner](https://modern-web.dev/docs/test-runner/), a project we have been working on for the past months.

There are already a lot of testing solutions out there today. Unfortunately, all of them either run tests in node js and mock browser APIs using something like JSDom or don't support native es modules out of the box. We think that making browser code compatible for testing in node is unnecessarily complex. Running tests in real browsers gives greater confidence in (cross-browser) compatibility and makes writing and debugging tests much simpler.

By building on top of our dev server, and modern browser launchers like Puppeteer and Playwright, we created a new test runner which fills this gap in the ecosystem. We think it is already feature-complete enough to be picked up by any web project.

Some highlights:

👉&nbsp;&nbsp; Headless browsers with [Puppeteer](https://modern-web.dev/docs/test-runner/browsers/puppeteer/), [Playwright](https://modern-web.dev/docs/test-runner/browsers/playwright/), or [Selenium](https://modern-web.dev/docs/test-runner/browsers/selenium/). <br>
🚧&nbsp;&nbsp; Reports logs, 404s, and errors from the browser. <br>
🔍&nbsp;&nbsp; Debug opens a real browser window with devtools.<br>
🔧&nbsp;&nbsp; Exposes browser properties like viewport size and dark mode.<br>
⏱&nbsp;&nbsp;Runs tests in parallel and isolation.<br>
👀&nbsp;&nbsp; Interactive watch mode.<br>
🏃&nbsp;&nbsp; Fast development by rerunning only changed tests.<br>
🚀&nbsp;&nbsp; Powered by [esbuild](https://modern-web.dev/docs/dev-server/esbuild.md) and [rollup plugins](https://modern-web.dev/docs/dev-server/rollup.md)

## Getting started with Web Test Runner

This is the minimal instruction on how to start using the test runner.

1. Install the test runner

   ```bash
   npm i -D @web/test-runner @open-wc/testing
   ```

2. Add a script to your `package.json`

   ```json
   {
     "scripts": {
       "test": "web-test-runner \"test/**/*.test.js\" --node-resolve",
       "test:watch": "web-test-runner \"test/**/*.test.js\" --node-resolve --watch"
     }
   }
   ```

3. Create a test file `test/sum.test.js`.

   ```js
   import { expect } from '@open-wc/testing';
   import { sum } from '../src/sum.js';

   it('sums up 2 numbers', () => {
     expect(sum(1, 1)).to.equal(2);
     expect(sum(3, 12)).to.equal(15);
   });
   ```

4. Create the src file `src/sum.js`

   ```js
   export function sum(a, b) {
     return a + b;
   }
   ```

5. Run it

   ```bash
   $ npm run test
   $ web-test-runner test/**/*.test.js --coverage --node-resolve

   Chrome: |██████████████████████████████| 1/1 test files | 1 passed, 0 failed

   Code coverage: 100 %
   View full coverage report at coverage/lcov-report/index.html

   Finished running tests in 0.9s, all tests passed! 🎉
   ```

See more instructions in the [getting-started](https://modern-web.dev/learn/test-runner/getting-started/) learn section.

## Watch and debug

Once you have the basics test running you can enjoy some of the more advanced features.

The same tests are run in watch mode but you do get multiple additional features

- Tests are rerun on file change (source or test)
- You can focus on a specific test file
- You can open a test file in the browser

Automatic rerunning is probably well known but what does it mean to "focus a specific test file"?
I'm glad you asked as it is one of the key features when working with many test files.
Often when working you want to focus only on ONE specific test file and with web test runner you can do that right in the terminal.

Start the test runner in watch mode and you will get a menu at the bottom

```
Finished running tests, watching for file changes...

Press F to focus on a test file.
Press D to debug in the browser.
Press Q to quit watch mode.
Press Enter to re-run all tests.
```

Now if you use `F` a menu will present itself with all the files you can focus

```
[1] test/calc.test.js
[2] test/sum.test.js
[3] test/multiply.test.js

Number of the file to focus: 2
```

Once a test file is focused you can also open it directly in the browser.

See more instructions in the [watch-and-debug](https://modern-web.dev/learn/test-runner/watch-and-debug/) learn section.

## Test in multiple browsers using playwright

If we want to run tests in all evergreen browser then [Microsoft Playwright](https://github.com/microsoft/playwright) might be for us.

We can install it via

```
npm i -D @web/test-runner-playwright
```

which will bring its own versions of Chromium, Firefox, and WebKit.

Once that is available we will activate via some flags in the command of the packages.json

```json
"test": "web-test-runner \"test/**/*.test.js\" --node-resolve --playwright --browsers chromium firefox webkit",
```

Now all that is left to start this bad boy.

```
$ yarn test
$ web-test-runner "test/**/*.test.js" --node-resolve --playwright --browsers chromium firefox webkit

Chromium: |██████████████████████████████| 2/2 test files | 3 passed, 0 failed
Firefox:  |██████████████████████████████| 2/2 test files | 3 passed, 0 failed
Webkit:   |██████████████████████████████| 2/2 test files | 3 passed, 0 failed

Finished running tests in 3.4s, all tests passed! 🎉
```

We execute 2 test files in 3 different real browsers.
If everything is green you can't get any more confident about your code. So ship it.

See more instructions in the [using-launchers](https://modern-web.dev/learn/test-runner/using-launchers/) learn section.

## Testing responsive views

With the world going mobile-first there needs to be a way of testing your mobile views.
Working with a real browser means you can directly change the viewport.

Let's assume we have some code that should only execute on mobile.
It would be nice to have some sort of functionality to check for it.

How about a function `isMobile()` and it returns true/false.

```js
describe('isMobile', () => {
  it('returns true if width < 1024px', async () => {
    expect(isMobile()).to.be.true;
  });

  it('returns false if width > 1024px', async () => {
    expect(isMobile()).to.be.false;
  });
});
```

It feels like something is missing in this test... 🤔
We need to have a way to run these two tests on different viewport sizes to verify if they work correctly.

For that we need to install a library:

```
npm i -D @web/test-runner-commands
```

With that, we get a `setViewport` method which we can put to good use.

```js
import { expect } from '@open-wc/testing';
import { setViewport } from '@web/test-runner-commands';
import { isMobile } from '../src/isMobile';

describe('isMobile', () => {
  it('returns true if width < 1024px', async () => {
    await setViewport({ width: 360, height: 640 });
    expect(isMobile()).to.be.true;
  });

  it('returns false if width > 1024px', async () => {
    await setViewport({ width: 1200, height: 640 });
    expect(isMobile()).to.be.false;
  });
});
```

If you want to know more like for example how to test CSS media queries see the [responsive](https://modern-web.dev/learn/test-runner/responsive/) learn section. See the [commands documentation](https://modern-web.dev/docs/test-runner/commands/) to learn more about commands.

## Supporting TypesScript and JSX via esbuild

...

See more instructions in the [typescript-jsx-esbuild](https://modern-web.dev/learn/test-runner/typescript-jsx-esbuild/) learn section.

## Enable your needs with custom plugins

...

See more instructions in the [writing-plugin](https://modern-web.dev/learn/test-runner/writing-plugin/) learn section.

<span>Photo by <a href="https://unsplash.com/@lemonvlad">Vladislav Klapin</a> on <a href="https://unsplash.com/s/photos/hello">Unsplash</a></span>
