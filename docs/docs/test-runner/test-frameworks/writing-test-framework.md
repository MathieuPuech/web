---
title: Writing a test framework
eleventyNavigation:
  key: Writing Test Framework
  parent: Test Frameworks
---

Writing a test framework is straightforward, you can wrap an existing framework or create a simple one specific for your project.

This is a minimal example:

```js
import {
  getConfig,
  sessionStarted,
  sessionFinished,
  sessionFailed,
} from '@web/test-runner-core/browser/session.js';

(async () => {
  // notify the test runner that we're alive
  sessionStarted();

  // fetch the config for this test run, this will tell you which file we're testing
  const { testFile, watch, debug, testFrameworkConfig } = await getConfig();
  const failedImports = [];

  // load the test file as an es module
  await import(new URL(testFile, document.baseURI).href).catch(error => {
    failedImports.push({ file: testFile, error: { message: error.message, stack: error.stack } });
  });

  try {
    // run the actual tests, this is what you need to implement
    const testResults = await runTests();

    // notify tests run finished
    sessionFinished({
      passed: failedImports.length === 0 && testResults.passed,
      failedImports,
      testResults,
    });
  } catch (error) {
    // notify an error occurred
    sessionFailed(error);
    return;
  }
})();
```
