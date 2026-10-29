# @fullsnacklab/tsconfig

## 2.0.0

### Major Changes

- Add DOM and non-DOM base, app, library, and extends presets. Replace the former
  `dom/monorepo/*` and `no-dom/monorepo/*` exports with `dom/extends/app`,
  `dom/extends/library`, `no-dom/extends/app`, and `no-dom/extends/library`.

  Update old monorepo imports to the appropriate app or library preset. Set
  `include` and `exclude` in your project's tsconfig so source paths resolve from
  your project. The presets include Bun types; library presets also include React
  and React DOM types, which consumers must install.
