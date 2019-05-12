# rfdc

Really Fast Deep Clone

![build passing](https://img.shields.io/travis/davidmarkclements/rfdc.svg)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](http://standardjs.com/)


## Usage

```js
const clone = require('rfdc')()
clone({a: 1, b: {c: 2}}) // => {a: 1, b: {c: 2}}
```

## API

### `require('rfdc')(opts = { proto: false, circles: false }) => clone(obj) => obj2`

#### `proto` option

Copy prototype properties as well as own properties into the new object.

It's marginally faster to allow enumerable properties on the prototype 
to be copied into the cloned object (not onto it's prototype, directly onto the object).

To explain by way of code:

```js
require('rfdc')({ proto: false })(Object.create({a: 1})) // => {}
require('rfdc')({ proto: true })(Object.create({a: 1})) // => {a: 1}
```

Setting `proto` to `true` will provide an additional 2% performance boost.

#### `circles` option

Keeping track of circular references will slow down performance with an
additional 25% overhead. Even if an object doesn't have any circular references,
the tracking overhead is the cost. By default if an object with a circular 
reference is passed to `rfdc`, it will throw (similar to how `JSON.stringify` \
would throw).

Use the `circles` option to detect and preserve circular references in the
object. If performance is important, try removing the circular reference from
the object (set to `undefined`) and then add it back manually after cloning
instead of using this option.

### Types

`rdfc` clones all JSON types:

* `Object`
* `Array`
* `Number`
* `String`
* `null`

With additional support for:

* `Date` (copied)
* `undefined` (copied)
* `Function` (referenced)
* `AsyncFunction` (referenced)
* `GeneratorFunction` (referenced)
* `arguments` (copied to a normal object)

All other types have output values that match the output
of `JSON.parse(JSON.stringify(o))`.

For instance:

```js
const rdfc = require('rdfc')()
const err = Error()
err.code = 1
JSON.parse(JSON.stringify(e)) // {code: 1}
rdfc(e) // {code: 1}

JSON.parse(JSON.stringify(new Uint8Array([1, 2, 3]))) //  {'0': 1, '1': 2, '2': 3 }
rdfc(new Uint8Array([1, 2, 3])) //  {'0': 1, '1': 2, '2': 3 }

JSON.parse(JSON.stringify({rx: /foo/})) // {rx: {}}
rdfc({rx: /foo/}) // {rx: {}}
```

## Benchmarks

```sh
npm run bench
```

```
benchDeepCopy*100: 549.618ms
benchLodashCloneDeep*100: 1461.134ms
benchFastCopy*100: 878.146ms
benchRfdc*100: 323.899ms
benchRfdcProto*100: 314.136ms
benchRfdcCircles*100: 384.561ms
benchRfdcCirclesProto*100: 381.775ms
```

## Tests

```sh
npm test
```

```
169 passing (342.514ms)
```

### Coverage

```sh
npm run cov
```

```
----------|----------|----------|----------|----------|-------------------|
File      |  % Stmts | % Branch |  % Funcs |  % Lines | Uncovered Line #s |
----------|----------|----------|----------|----------|-------------------|
All files |      100 |      100 |      100 |      100 |                   |
 index.js |      100 |      100 |      100 |      100 |                   |
----------|----------|----------|----------|----------|-------------------|
```

## License

MIT
