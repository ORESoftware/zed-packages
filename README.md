# zed-packages

Organization package index for `oresoftware/*` Zed packages.

`zed-cli` resolves a package by guessing `github.com/<org>/<name>` and, failing that, searching within the organization. Neither can find a package whose repository is not named after it — `oresoftware/next-loggers` is published from `ores-otel/ores.otel.log`, and a repository name containing dots is not a valid package slug at all.

This index declares those mappings. It is read over `raw.githubusercontent.com`, not the GitHub API, so it keeps working when the unauthenticated API budget is exhausted — which is exactly when the registry origin is down and the fallback matters.

## Contract

`packages.json`:

```json
{
  "schema": "zed.org-package-index/v1",
  "packages": {
    "<package name>": { "repository": "https://github.com/<owner>/<repo>" }
  }
}
```

Only packages whose repository cannot be guessed need an entry.

## Why an entry cannot hijack a package

An entry **locates** a repository; it does not confer identity. `zed-cli` still reads the committed `.zpkg.toml` at that repository and requires it to self-claim the exact `org/name` being resolved, and every artifact is still verified against the digest pinned in the consumer's lockfile.

Disable lookups with `ZED_PKG_ORG_PACKAGE_INDEX=0`; point them at a different index repository with `ZED_PKG_ORG_PACKAGE_INDEX_REPO`.
