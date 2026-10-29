# @fullsnacklab/tsconfig

Small, shared TypeScript presets based on [Bunup templates](https://github.com/bunup/templates).
DOM support, library declarations, and React are explicit choices.

```text
shared/
  base.json
dom/
  app.json
  library.json
  react.json
no-dom/
  library.json
```

## Install

```sh
bun add --dev @fullsnacklab/tsconfig
```

## Choose a preset

| Preset | Purpose |
| --- | --- |
| `shared/base.json` | Shared compiler settings with ESNext libraries, without DOM or JSX |
| `dom/app.json` | Browser apps, without declaration requirements or React assumptions |
| `dom/library.json` | Browser libraries, with declaration and isolated-declaration settings |
| `no-dom/library.json` | Non-browser libraries, with declaration and isolated-declaration settings |
| `dom/react.json` | Opt-in automatic React JSX; adds neither DOM nor library settings |

The base-derived presets use ES2023 output targeting, Preserve modules, bundler resolution,
strict checking, and `noEmit`. The ESNext library choice follows the Bunup
monorepo template; it describes available types, not runtime polyfills.
Bunup owns library output and declaration generation.

### Non-browser library

```json
{
  "extends": "@fullsnacklab/tsconfig/no-dom/library.json",
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Browser app

```json
{
  "extends": "@fullsnacklab/tsconfig/dom/app.json",
  "include": ["src/**/*"]
}
```

### React component library

```json
{
  "extends": [
    "@fullsnacklab/tsconfig/dom/library.json",
    "@fullsnacklab/tsconfig/dom/react.json"
  ],
  "include": ["src/**/*"]
}
```

Install React and its types in the consuming project. The React overlay supplies
only `jsx: react-jsx`. Vite, Astro, React Router, and other framework-specific
configuration and generated types remain owned by the project. Keep any required
framework config inheritance when adding these presets.

## Monorepos: extend your workspace root

Use the same presets, following the Bunup template's local inheritance pattern.
There is no separate published monorepo preset.

```text
my-workspace/
├── tsconfig.base.json
└── packages/
    └── my-library/
        ├── tsconfig.json
        └── src/
```

Workspace `tsconfig.base.json`:

```json
{
  "extends": "@fullsnacklab/tsconfig/dom/app.json"
}
```

Use `shared/base.json` instead for a non-DOM workspace. Choose React at the workspace
root only when it is shared policy; otherwise opt in at individual packages.

`packages/my-library/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "declaration": true,
    "isolatedDeclarations": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

An app can inherit the root without adding declaration settings. An `app/`
directory directly beneath the workspace root uses `../tsconfig.base.json`.

## Consumer-owned settings

Keep source globs, output paths, aliases, ambient `types`, and project references
in the consumer. Relative paths resolve from the config that defines them.
In an `extends` array, later configs win conflicts; local compiler options win
last. Arrays such as `lib` and `types` replace inherited arrays.

The common base omits DOM libraries explicitly. Consumers can still introduce
browser globals through dependencies or ambient types; choose those deliberately.

Check your project with:

```sh
bun check
```

## Maintaining this package

These presets use TypeScript's conventional `.json` filenames with JSONC comments.
Package-name `extends` resolves these paths. Comments explain intentional policy;
use a JSONC-aware reader rather than `JSON.parse`. No build step is needed.

The manifest exports `.json` paths and extensionless aliases for the supported
presets. Monorepos use the local-root inheritance pattern above. Consumers of the
former monorepo exports must migrate to that pattern before upgrading.
Removing the former paths requires a **major release**, not another `1.x` release.
Consumers need a compiler supporting `module: Preserve`, array `extends`, and
`isolatedDeclarations` (TypeScript 5.5 or newer). Consumer checks here use Bun 1.4.3's
native `bun check`; do not shadow it with a package script named `check`.

### Release gate

Use Bun 1.4.3 (`bun install --frozen-lockfile`) and retain Changesets for versioning.
CI inspects the package with `bun pm pack --dry-run --ignore-scripts` without publishing.
Before release, record the major Changeset/version, install the actual tarball in
clean consumers, and review its contents and SHA-256 digest.

npm trusted publishing is **not registered** for this repository. The manual
`release.yml` is disabled unless `NPM_TRUSTED_PUBLISHING_ENABLED` is set to `true`,
and separately rejects versions below `2.0.0`. Only enable it after registering
the npm trusted publisher for this repository and workflow and approving the release.
It uses Bun to install/pack and Node 24's npm CLI for OIDC publishing; no npm token
is required. No publication or consumer migration has been performed.

## Acknowledgments

Compiler policy follows [Bunup templates](https://github.com/bunup/templates).
Earlier scaffolding drew on Matt Pocock's
[Total TypeScript tsconfig](https://github.com/total-typescript/tsconfig).
