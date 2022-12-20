# copy-node-modules


Copy all modules listed in `dependencies` or/and `devDependencies` field of `package.json` to destination directory. 

The procedure:

1. Read `package.json` from source directory and read `dependencies` or `devDependencies` field.
2. Search for existing modules and ther dependencies in source directory.
3. Copy all modules to destination directory.

Modern applications use lots of modules, each module depends on more modules resulting in hundreds of modules being installed when typing `npm install`. This module will help you to save time from slow `npm install` when you want to pack/deploy your application to a directory which contains all needed modules. 

It will save you a bunch of time to deploy a stand-alone application from existing development directory, no need to fetch all modules from remote repository.

## TODO:
rewrite everything in typescript
## Installation

```bash
yarn add @bitc/copy-node-modules -D
```

or

```bash
npm install @bitc/copy-node-modules -D
```

## Programmatic Usage

ES6+ environment:

```javascript
const copyNodeModules = require('@bitc/copy-node-modules');
```

ES6+ environment with `import` support:

```javascript
import copyNodeModules from '@bitc/copy-node-modules'; 
```

ES5

```javascript
var copyNodeModules = require('@bitc/copy-node-modules');
```

### copyNodeModules(srcDir, dstDir, [options], callback)

* `srcDir`: source directory containing `package.json` file.
* `dstDir`: destination directory to copy modules to (modules will be copied to `dstDir/node_modules` directory).
* `options`:

  - `packagePath`: path to package.json to use, maybe you have a custom dependencies define somewhere and you will like to copy the dependencies base on the package.json file
  - `devDependencies`: boolean value, defaults to **false**, showing whether modules in `devDependencies` field of `package.json` should also be copied (when it's set to **true**).
  - `concurrency`: integer value, max number of root packages whose files are being copied concurrently.
  - `filter`: `RegExp` or function that accepts one value (the full path) and returns a boolean (copy on true).
  
* `callback(err, results)`: A callback which is called when all copy tasks have finished or error occurs, `results` is an array contains copied modules, each item is an object as `{name: 'xxx', version: 'xxx'}`

## Examples

```javascript
const copyNodeModules = require('@bitc/copy-node-modules');
const srcDir = '/somewhere/project';
const dstDir = '/somewhere/project/dist';
copyNodeModules(srcDir, dstDir, { devDependencies: false }, (err, results) => {
  if (err) {
    console.error(err);
    return;
  }
  Object.keys(results).forEach(name => {
    const version = results[name];
    console.log(`Package name: ${name}, version: ${version}`);
  });
});
```

### Example with a filter method

```javascript
const copyNodeModules = require('@bitc/copy-node-modules');
const srcDir = '/somewhere/project';
const dstDir = '/somewhere/project/dist';

// Filter method that will ignore node_module folders in a node module
const filter = path => {
  const firstIndex = path.indexOf('node_modules');
  return v.indexOf('node_modules', firstIndex + 1) === -1;
}

copyNodeModules(srcDir, dstDir, { devDependencies: false, filter }, (err, results) => {
  if (err) {
    console.error(err);
    return;
  }
  Object.keys(results).forEach(name => {
      const version = results[name];
      console.log(`Package name: ${name}, version: ${version}`);
    });
});
```

## CLI Usage

```
copy-node-modules src_dir dest_dir [-d|--dev] [-c|--concurrency] [-v|--verbose] [-f|--filter]
```

* `src_dir`: source directory containing `package.json` file.
* `dest_dir`: destination directory to copy modules to (modules will be copied to `dest_dir/node_modules` directory).
* `-d|--dev`: whether modules in `devDependencies` field of `package.json` should be also copied.
* `-c|--concurrency`: max number of root packages whose files are being copied concurrently.
* `-v|--verbose`: verbose mode.
* `-f|--filter`: regular Expression, files that match this expression will be copied; it also matches directories fi:
    -f `index.html` matches `path/index.html` but not `path/` and because of this `index.html` is not copied.

## License

MIT
