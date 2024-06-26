<div align='center'>
<h2>conditional-export</h2>

[![NPM version](https://img.shields.io/npm/v/conditional-export.svg?color=a1b858&label=)](https://www.npmjs.com/package/conditional-export)

</div>

Find entry or path in package.json exports or imports.
> https://nodejs.org/docs/latest-v12.x/api/packages.html#packages_exports

- [`findEntryInExports`](#findentryinexports)
- [`findPathInExports`](#findpathinexports)
- [`findPathInImports `](#findpathinimports)
- [`findPkgData`](#findpkgdata)
- [`parseModuleId`](#parsemoduleid)


## Usage

Apis usage description.

> Debugging platform: https://imtaotao.github.io/conditional-export/


### findEntryInExports

```js
import { findEntryInExports } from 'conditional-export';

const exports = {
  '.': {
    require: './index.cjs',
    development: './index.development.js',
    default: './index.js',
  },
}

findEntryInExports(exports); // ./index.cjs
findEntryInExports(exports, ['development']); // ./index.development.js
findEntryInExports(exports, ['production']); // ./index.js
```


### findPathInExports

```js
import { findPathInExports } from 'conditional-export';

const exports = {
  './lib/*': {
    require: './src/*.cjs',
    development: './src/*.development.js',
    default: './src/*.js',
  },
}

findPathInExports('./lib/index', exports); // ./src/index.cjs
findPathInExports('./lib/index', exports, ['development']); // ./src/index.development.js
findPathInExports('./lib/index', exports, ['production']); // ./src/index.js
```

Multiple conditions, default conditions is `['require']`.

> You can specify multiple conditions.

```js
import { findPathInExports } from 'conditional-export';

const exports = {
  './a': {
    node: {
      import: './feature-node.mjs',
      require: './feature-node.cjs',
    },
    default: './feature.default.mjs',
  }
};

findPathInExports('./a', exports); // './feature.default.mjs'
findPathInExports('./a', exports, ['node', 'require']); // './feature-node.cjs'
```


### findPathInImports

```js
import { findPathInImports } from 'conditional-export';

const imports = {
  '#timezones/': './data/timezones/',
  '#timezones/utc': './data/timezones/utc/index.mjs',
  '#external-feature': 'external-pkg/feature',
  '#moment/': './'
}

findPathInImports('#timezones/utc', imports); // ./data/timezones/utc/index.mjs
findPathInImports('#external-feature', imports); // external-pkg/feature
findPathInImports('#timezones/utc/index.mjs', imports); // ./data/timezones/utc/index.mjs
findPathInImports('#moment/data/timezones/utc/index.mjs', imports); // ./data/timezones/utc/index.mjs
findPathInImports('#timezones/utc/', imports); // ./data/timezones/utc/
findPathInImports('#unknown', imports); // null
findPathInImports('#moment', imports); // null
```



### findPkgData

```js
import { findPkgData } from 'conditional-export';

const exports = {
  './': './src/util/',
  './timezones/': './data/timezones/',
  './timezones/utc': './data/timezones/utc/index.mjs',
}

const data = findPkgData('@vue/core/timezones/pdt.mjs', exports);
// {
//   name: '@vue/core',
//   version: '',
//   path: './data/timezones/pdt.mjs',
//   resolve: '@vue/core/data/timezones/pdt.mjs',
//   raw: '@vue/core/timezones/pdt.mjs',
// }
```


### parseModuleId

```js
import { parseModuleId } from 'conditional-export';

parseModuleId('vue')
// {
//   name: 'vue',
//   path: '',
//   version: '',
//   raw: 'vue',
// }

parseModuleId('vue/');
// {
//   name: 'vue',
//   path: './',
//   version: '',
//   raw: 'vue/',
// }

parseModuleId('@vue/core@v1.0.0/a.js');
// {
//   name: '@vue/core',
//   path: './a.js',
//   version: 'v1.0.0',
//   raw: '@vue/core@v1.0.0/a.js',
// }
```


## Extended usage

When you want to convert to absolute path, you can handle it like this.

```js
import { findPkgData } from 'conditional-export';

const data = findPkgData('vue/src/index.js', exports, ['require', ...]);

if (data.path !== null) {
  const resolvePath = isNodeEnv
    ? path.resolve(pkgDir, data.path) // NodeJs
    : new URL(data.path, pkgDir).href; // Browser
} else {
  // If the module doesn't exist, you should throw an error.
  throw new Error(`Module '${data.raw}' Not Found`);
}
```


## CDN

```html
<!DOCTYPE html>
<html lang='en'>
<body>
  <script src='https://unpkg.com/conditional-export/dist/entry.umd.js'></script>
  <script>
    const {
      findPkgData,
      findPathInImports,
      findPathInExports,
      findEntryInExports,
      parseModuleId,
    } = NodePackageExports;
    
    // ...
  </script>
</body>
</html>
```
