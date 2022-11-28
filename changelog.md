# changelog

## npm2pnpm

because pnpm is just better than npm ...

- change `file:` to `link:` protocol
- move packages to `packages/`
- add `pnpm-workspace.yaml`
- fix import paths
  - constants.ts
  - vite-default-config.ts

## pnpm2nx

### build order

limitation of pnpm: cannot resolve build order from dependencies

https://stackoverflow.com/questions/72216431/how-to-control-pnpm-workspace-build-order

we must first build the `helpers` package

currently we do

packages/footer/package.json

```json
{
  "scripts": {
    "prebuild": "npm run -w helpers build"
  }
}
```

or

packages/gallery/package.json

```json
{
  "scripts": {
    "prebuild": "cd ../helpers && npm run build"
  }
}
```

### build speed

also `npm run build` seems rather slow, takes 2 minutes with pnpm

### nx

https://www.npmjs.com/package/nx

```
npx add-nx-to-monorepo@latest
```
