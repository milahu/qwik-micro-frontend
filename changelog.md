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

https://nx.dev/recipes/adopting-nx/adding-to-monorepo

```
npx add-nx-to-monorepo
```

#### build all projects

```
npx nx run-many --target=build
```

docs: https://nx.dev/getting-started/package-based-repo-tutorial#running-multiple-tasks

#### remove prebuild scripts

now i can remove the prebuild scripts,
and nx will autodetect the right build order

```
npx nx run-many --target=build

    ✔  nx run helpers:build (32s)
    ✔  nx run gallery:build (53s)
    ✔  nx run filter:build (54s)
    ✔  nx run footer:build (56s)
    ✔  nx run body:build (1m)
    ✔  nx run header:build (1m)
    ✔  nx run main:build (56s)
```

#### dependency graph

i can see the dependency graph with

```
npx nx graph
```

&rarr; all packages depend on helpers

docs: https://nx.dev/core-features/explore-graph

#### builds are slow

problem: builds are slow,
because builds are not cached

https://nx.dev/recipes/other/troubleshoot-cache-misses

https://nx.dev/core-features/cache-task-results

add build to cacheableOperations in nx.json

```json
{
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build"]
      }
    }
  },
  "targetDefaults": {},
  "defaultBase": "main"
}
```

- first build 2m51.052s
- rebuild 0m7.153s

awesome : D
