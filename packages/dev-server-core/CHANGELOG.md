# @web/dev-server-core

## 0.2.1

### Patch Changes

- aa65fd1: run build before publishing

## 0.2.0

### Minor Changes

- 9be1f95: Added native node es module entrypoints. This is a breaking change. Before, native node es module imports would import a CJS module as a default import and require destructuring afterwards:

  ```js
  import playwrightModule from '@web/test-runner-playwright';

  const { playwrightLauncher } = playwrightModule;
  ```

  Now, the exports are only available directly as a named export:

  ```js
  import { playwrightLauncher } from '@web/test-runner-playwright';
  ```

### Patch Changes

- 96dd279: watch the HTML file for inline scripts
- 62ff8b2: make tests work on windows

## 0.1.9

### Patch Changes

- 8fb820b: add an easy way to change served mime types

## 0.1.8

### Patch Changes

- 04a2cda: make test-runner-browserstack work with dev-server-core

## 0.1.7

### Patch Changes

- 9712125: fix not watching files with syntax errors

## 0.1.6

### Patch Changes

- 8892f98: allow downstream middleware to serve files

## 0.1.5

### Patch Changes

- 8596276: move logger to test runner cli

## 0.1.4

### Patch Changes

- db5baff: cleanup and sort dependencies

## 0.1.3

### Patch Changes

- c104663: run legacy plugin after resolving imports

## 0.1.2

### Patch Changes

- 2672e8a: expose isInlineScriptRequest function

## 0.1.1

### Patch Changes

- 59d3efe: remove dependency on building-utils

## 0.1.0

### Minor Changes

- c4cb321: Use web dev server in test runner. This contains multiple breaking changes:

  - Browsers that don't support es modules are not supported for now. We will add this back later.
  - Most es-dev-server config options are no longer available. The only options that are kept are `plugins`, `middleware`, `nodeResolve` and `preserveSymlinks`.
  - Test runner config changes:
    - Dev server options are not available on the root level of the configuration file.
    - `nodeResolve` is no longer enabled by default. You can enable it with the `--node-resolve` flag or `nodeResolve` option.
    - `middlewares` option is now called `middleware`.
    - `testFrameworkImport` is now called `testFramework`.
    - `address` is now split into `protocol` and `hostname`.

## 0.0.3

### Patch Changes

- 9302247: allow plugins to set a transform cache key

## 0.0.2

### Patch Changes

- c5da67f: always deduce mime type

## 0.0.1

### Patch Changes

- a65e3c9: first setup
