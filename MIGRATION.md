# Moving to `@talisdev/jsonapi-transformers`

This library has moved from the unscoped `jsonapi-transformers` package, published under a
personal npm account, to `@talisdev/jsonapi-transformers`, published from the Talis org's own
CI. The GitHub repository moved from a personal account to
[talis/jsonapi-transformers](https://github.com/talis/jsonapi-transformers) at the same time.

## What changes for consumers

Update the dependency name and reinstall:

```diff
- "jsonapi-transformers": "5.0.7"
+ "@talisdev/jsonapi-transformers": "5.0.8"
```

```diff
- import { ... } from "jsonapi-transformers";
+ import { ... } from "@talisdev/jsonapi-transformers";
```

No API changes accompany the rename. The first `@talisdev` release (`5.0.8`) is built from the
same source as the last unscoped release (`5.0.7`); only the package metadata and publish
pipeline changed. Anything beyond `5.0.8` may include real changes and should be reviewed as
normal via [CHANGELOG.md](CHANGELOG.md).

If your project maps the `@talis` npm scope to a different registry (for example GitHub
Packages), double check it does not also apply to `@talisdev` - the two are unrelated scopes and
`@talisdev` packages are published to the public npm registry.

## What does not change

- The unscoped `jsonapi-transformers` package on npm is not removed. Its existing published
  versions (`5.0.1` through `5.0.7`) remain installable indefinitely; npm does not allow removing
  a package once it has been public for more than 72 hours, and there is no reason to. It is
  marked deprecated, pointing here, so a fresh `npm install jsonapi-transformers` warns rather
  than fails.
- The exported API, TypeScript types and JSON:API behaviour are unchanged by this move.

