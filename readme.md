# `@fullsnacklab/tsconfig`

`tsconfig.json` comes down to a few decisions: whether the project runs in the DOM with React, and whether it is an app, a library, or a package in a monorepo.

## Setup

1. Install:

```bash
bun add --dev @fullsnacklab/tsconfig
```

2. Choose a config from the [list](#list-of-tsconfigs) below.

3. Add it to your `tsconfig.json`:

```jsonc
{
  // React app
  "extends": "@fullsnacklab/tsconfig/dom/app"
}
```

Please don't use `tsc`. Instead, use `bun check` or `bun run build --check` in your projects.

## List of TSConfigs

### React

These configs are for projects that run in the DOM with React. They set `"jsx": "react-jsx"`.

```jsonc
{
  "extends": "@fullsnacklab/tsconfig/dom/app", // React app
  "extends": "@fullsnacklab/tsconfig/dom/library", // React library
  "extends": "@fullsnacklab/tsconfig/dom/monorepo/base", // Shared base for a React monorepo
  "extends": "@fullsnacklab/tsconfig/dom/monorepo/tsconfig" // Package in a React monorepo
}
```

In a monorepo, the root `tsconfig.base.json` extends `dom/monorepo/base`. Each package extends that base:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "declaration": true,
    "isolatedDeclarations": true
  },
  "include": ["src/**/*"]
}
```

### No DOM

These configs are for projects that do not run in the DOM, such as Node.js.

```jsonc
{
  "extends": "@fullsnacklab/tsconfig/no-dom/library", // Library
  "extends": "@fullsnacklab/tsconfig/no-dom/monorepo/base", // Shared base for a monorepo
  "extends": "@fullsnacklab/tsconfig/no-dom/monorepo/tsconfig" // Package in a monorepo
}
```

In a monorepo, the root `tsconfig.base.json` extends `no-dom/monorepo/base`. Each package extends that base:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "declaration": true,
    "isolatedDeclarations": true
  },
  "include": ["src/**/*"]
}
```

## Options Not Covered

### `jsx`

The React configs already set `jsx`. Override it when a project needs a different mode:

```json
{
  "extends": "@fullsnacklab/tsconfig/dom/app",
  "compilerOptions": {
    "jsx": "react-jsx"
  }
}
```

### Framework-Specific Options

Framework-specific options, such as those for Vite, Next, Remix, and Astro, are left to the project.

## Why Not Use `@tsconfig/bases`?

[`@tsconfig/bases`](https://github.com/tsconfig/bases) is a useful set of TypeScript configurations. This package keeps a smaller set of choices so the configs can stay aligned with the projects that use them.

## Acknowledgment

This package is based on Matt Pocock's [`@total-typescript/tsconfig`](https://github.com/total-typescript/tsconfig) and the TSConfig Cheat Sheet.
