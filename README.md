# nanomatch [![NPM version](https://img.shields.io/npm/v/nanomatch.svg?style=flat)](https://www.npmjs.com/package/nanomatch) [![NPM monthly downloads](https://img.shields.io/npm/dm/nanomatch.svg?style=flat)](https://npmjs.org/package/nanomatch)  [![NPM total downloads](https://img.shields.io/npm/dt/nanomatch.svg?style=flat)](https://npmjs.org/package/nanomatch) [![Linux Build Status](https://img.shields.io/travis/jonschlinkert/nanomatch.svg?style=flat&label=Travis)](https://travis-ci.org/jonschlinkert/nanomatch) [![Windows Build Status](https://img.shields.io/appveyor/ci/jonschlinkert/nanomatch.svg?style=flat&label=AppVeyor)](https://ci.appveyor.com/project/jonschlinkert/nanomatch)

> Fast, minimal glob matcher for node.js. Similar to micromatch, minimatch and multimatch, but complete Bash 4.3 wildcard support only (no support for exglobs, posix brackets or braces)

## Table of Contents

- [What is nanomatch?](#what-is-nanomatch)
- [Getting started](#getting-started)
- [API](#api)
- [Features](#features)
  * [Globbing reference](#globbing-reference)
  * [Bash pattern matching](#bash-pattern-matching)
- [Benchmarks](#benchmarks)
  * [Running benchmarks](#running-benchmarks)
  * [Latest results](#latest-results)
- [History](#history)
- [About](#about)
  * [Related projects](#related-projects)
  * [Contributing](#contributing)
  * [Running tests](#running-tests)
  * [Author](#author)
  * [License](#license)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_

## What is nanomatch?

Nanomatch is a fast and accurate glob matcher with full support for standard Bash glob features, including the following "metacharacters": `*`, `**`, `?` and `[...]`.

See the [features section](#features) for more info about features.

**How is this different from [micromatch](https://github.com/jonschlinkert/micromatch)?**

Nanomatch only provides wildcard matching, which represents only 1 of the 5 matching "types" offered by micromatch. The others are listed in the [features](#features) section.

Nanomatch will also provide the wildcard matching functionality to micromatch, starting with v3.0.0.

## Getting started

```js
var nanomatch = require('nanomatch');

// the main export is a function that takes an array of strings to match
// and one or more patterns to use for matching
nanomatch(list, patterns[, options]);
```

**Params**

* `list` **{String|Array}**: One or more strings to match against. This is often a list of files.
* `patterns` **{String|Array}**: One or more [glob paterns](#features) to use for matching.
* `options` **{Object}**: Visit the API to learn about available options.

**Example**

```js
var nm = require('nanomatch');
console.log(nm(['a', 'b/b', 'c/c/c'], '*'));
//=> ['a']

console.log(nm(['a', 'b/b', 'c/c/c'], '*/*'));
//=> ['b/b']

console.log(nm(['a', 'b/b', 'c/c/c'], '**'));
//=> ['a', 'b/b', 'c/c/c']
```

Additional detail provided in the [API documentation](#api).

## API

### [nanomatch](index.js#L37)

The main function takes a list of strings and one or more glob patterns to use for matching.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch(['a.js', 'a.txt'], ['*.js']));
//=> [ 'a.js' ]
```

**Params**

* `list` **{Array}**
* `patterns` **{String|Array}**: Glob patterns
* `options` **{Object}**
* `returns` **{Array}**: Returns an array of matches

### [.match](index.js#L103)

Similar to the main function, but `pattern` must be a string.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.match(['a.a', 'a.aa', 'a.b', 'a.c'], '*.a'));
//=> ['a.a', 'a.aa']
```

**Params**

* `list` **{Array}**: Array of strings to match
* `pattern` **{String}**: Glob pattern
* `options` **{Object}**
* `returns` **{Array}**: Returns an array of matches

### [.isMatch](index.js#L165)

Returns true if the specified `string` matches the given glob `pattern`.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.isMatch('a.a', '*.a'));
//=> true
console.log(nanomatch.isMatch('a.b', '*.a'));
//=> false
```

**Params**

* `string` **{String}**: String to match
* `pattern` **{String}**: Glob pattern
* `options` **{String}**
* `returns` **{Boolean}**: Returns true if the string matches the glob pattern.

### [.not](index.js#L192)

Returns a list of strings that do _not_ match any of the given `patterns`.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.not(['a.a', 'b.b', 'c.c'], '*.a'));
//=> ['b.b', 'c.c']
```

**Params**

* `list` **{Array}**: Array of strings to match.
* `pattern` **{String}**: One or more glob patterns.
* `options` **{Object}**
* `returns` **{Array}**: Returns an array of strings that do not match the given patterns.

### [.any](index.js#L227)

Returns true if the given `string` matches any of the given glob `patterns`.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.any('a.a', ['b.*', '*.a']));
//=> true
console.log(nanomatch.any('a.a', 'b.*'));
//=> false
```

**Params**

* `str` **{String}**: The string to test.
* `patterns` **{String|Array}**: Glob patterns to use.
* `options` **{Object}**: Options to pass to the `matcher()` function.
* `returns` **{Boolean}**: Returns true if any patterns match `str`

### [.contains](index.js#L255)

Returns true if the given `string` contains the given pattern. Similar to `.isMatch` but the pattern can match any part of the string.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.contains('aa/bb/cc', '*b'));
//=> true
console.log(nanomatch.contains('aa/bb/cc', '*d'));
//=> false
```

**Params**

* `str` **{String}**: The string to match.
* `pattern` **{String}**: Glob pattern to use for matching.
* `options` **{Object}**
* `returns` **{Boolean}**: Returns true if the patter matches any part of `str`.

### [.matchKeys](index.js#L295)

Filter the keys of the given object with the given `glob` pattern and `options`. Does not attempt to match nested keys. If you need this feature, use [glob-object](https://github.com/jonschlinkert/glob-object) instead.

**Example**

```js
var nanomatch = require('nanomatch');
var obj = { aa: 'a', ab: 'b', ac: 'c' };
console.log(nanomatch.matchKeys(obj, '*b'));
//=> { ab: 'b' }
```

**Params**

* `object` **{Object}**
* `patterns` **{Array|String}**: One or more glob patterns.
* `returns` **{Object}**: Returns an object with only keys that match the given patterns.

### [.matcher](index.js#L322)

Creates a matcher function from the given glob `pattern` and `options`. The returned function takes a string to match as its only argument.

**Example**

```js
var nanomatch = require('nanomatch');
var isMatch = nanomatch.matcher('*.!(*a)');

console.log(isMatch('a.a'));
//=> false
console.log(isMatch('a.b'));
//=> true
```

**Params**

* `pattern` **{String}**: Glob pattern
* `options` **{String}**
* `returns` **{Function}**: Returns a matcher function.

### [.makeRe](index.js#L377)

Create a regular expression from the given glob `pattern`.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.makeRe('*.js'));
//=> /^(?:(\.[\\\/])?(?!\.)(?=.)[^\/]*?\.js)$/
```

**Params**

* `pattern` **{String}**: The pattern to convert to regex.
* `options` **{Object}**
* `returns` **{RegExp}**: Returns a regex created from the given pattern.

### [.create](index.js#L435)

Parses the given glob `pattern` and returns an object with the compiled `output` and optional source `map`.

**Example**

```js
var nanomatch = require('nanomatch');
console.log(nanomatch.create('abc/*.js'));
// { options: { source: 'string', sourcemap: true },
//   state: {},
//   compilers:
//    { ... },
//   output: '(\\.[\\\\\\/])?abc\\/(?!\\.)(?=.)[^\\/]*?\\.js',
//   ast:
//    { type: 'root',
//      errors: [],
//      nodes:
//       [ ... ],
//      dot: false,
//      input: 'abc/*.js' },
//   parsingErrors: [],
//   map:
//    { version: 3,
//      sources: [ 'string' ],
//      names: [],
//      mappings: 'AAAA,GAAG,EAAC,kBAAC,EAAC,EAAE',
//      sourcesContent: [ 'abc/*.js' ] },
//   position: { line: 1, column: 28 },
//   content: {},
//   files: {},
//   idx: 6 }
```

**Params**

* `pattern` **{String}**: Glob pattern
* `options` **{Object}**
* `returns` **{Object}**: Returns an object with the parsed AST, compiled string and optional source map.

### [.parse](index.js#L472)

Parse the given `str` with the given `options`.

**Example**

```js
var nanomatch = require('nanomatch');
var ast = nanomatch.parse('a/{b,c}/d');
console.log(ast);
// { type: 'root',
//   errors: [],
//   input: 'a/{b,c}/d',
//   nodes:
//    [ { type: 'bos', val: '' },
//      { type: 'text', val: 'a/' },
//      { type: 'brace',
//        nodes:
//         [ { type: 'brace.open', val: '{' },
//           { type: 'text', val: 'b,c' },
//           { type: 'brace.close', val: '}' } ] },
//      { type: 'text', val: '/d' },
//      { type: 'eos', val: '' } ] }
```

**Params**

* `str` **{String}**
* `options` **{Object}**
* `returns` **{Object}**: Returns an AST

### [.compile](index.js#L523)

Compile the given `ast` or string with the given `options`.

**Example**

```js
var nanomatch = require('nanomatch');
var ast = nanomatch.parse('a/{b,c}/d');
console.log(nanomatch.compile(ast));
// { options: { source: 'string' },
//   state: {},
//   compilers:
//    { eos: [Function],
//      noop: [Function],
//      bos: [Function],
//      brace: [Function],
//      'brace.open': [Function],
//      text: [Function],
//      'brace.close': [Function] },
//   output: [ 'a/(b|c)/d' ],
//   ast:
//    { ... },
//   parsingErrors: [] }
```

**Params**

* `ast` **{Object|String}**
* `options` **{Object}**
* `returns` **{Object}**: Returns an object that has an `output` property with the compiled string.

## Features

Nanomatch has full support for standard Bash glob features, including the following "metacharacters": `*`, `**`, `?` and `[...]`.

### Globbing reference

Here are some examples of how they work:

| **Pattern** | **Description** | 
| --- | --- |
| `*` | Matches any string except for `/`, leading `.`, or `/.` inside a path |
| `**` | Matches any string including `/`, but not a leading `.` or `/.` inside a path. More than two stars (e.g. `***` is treated the same as one star, and `**` loses its special meaning | when it's not the only thing in a path segment, per Bash specifications) |
| `foo*` | Matches any string beginning with `foo` |
| `*bar*` | Matches any string containing `bar` (beginning, middle or end) |
| `*.min.js` | Matches any string ending with `.min.js` |
| `[abc]*.js` | Matches any string beginning with `a`, `b`, or `c` and ending with `.js` |
| `abc?` | Matches `abcd` or `abcz` but not `abcde` |

The exceptions noted for `*` apply to all patterns that contain a `*`.

**Not supported**

The following extended-globbing features are not supported:

* [brace expansion](https://github.com/jonschlinkert/braces) (e.g. `{a,b,c}`)
* [extglobs](https://github.com/jonschlinkert/extglob) (e.g. `@(a|!(c|d))`)
* [POSIX brackets](https://github.com/jonschlinkert/expand-brackets) (e.g. `[[:alpha:][:digit:]]`)

If you need any of these features consider using [micromatch](https://github.com/jonschlinkert/micromatch) instead.

### Bash pattern matching

Nanomatch is part of a suite of libraries aimed at bringing the power and expressiveness of [Bash's](https://www.gnu.org/software/bash/) matching and expansion capabilities to JavaScript, _and - as you can see by the [benchmarks](#benchmarks) - without sacrificing speed_.

| **Related library** | **Matching Type** | **Example** | **Description** | 
| --- | --- | --- | --- | --- | --- |
| `nanomatch` (you are here) | Wildcards | `*` | [Filename expansion](https://www.gnu.org/software/bash/manual/html_node/Filename-Expansion.html#Filename-Expansion), also referred to as globbing and pathname expansion, allows the use of [wildcards](#features) for matching. |
| [expand-tilde](https://github.com/jonschlinkert/expand-tilde) | Tildes | `~` | [Tilde expansion](https://www.gnu.org/software/bash/manual/html_node/Tilde-Expansion.html#Tilde-Expansion) converts the leading tilde in a file path to the user home directory. |
| [braces](https://github.com/jonschlinkert/braces) | Braces | `{a,b,c}` | [Brace expansion](https://www.gnu.org/software/bash/manual/html_node/Brace-Expansion.html) |
| [expand-brackets](https://github.com/jonschlinkert/expand-brackets) | Brackets | `[[:alpha:]]` | [POSIX character classes](https://www.gnu.org/software/grep/manual/html_node/Character-Classes-and-Bracket-Expressions.html) (also referred to as POSIX brackets, or POSIX character classes) |
| [extglob](https://github.com/jonschlinkert/extglob) | Parens | `!(a | b)` | [Extglobs](https://www.gnu.org/software/bash/manual/html_node/Pattern-Matching.html#Pattern-Matching) |
| [micromatch](https://github.com/jonschlinkert/micromatch) | All | all | Micromatch is built on top of the other libraries. |

There are many resources available on the web if you want to dive deeper into how these features work in Bash.

## Benchmarks

### Running benchmarks

Install dev dependencies:

```bash
npm i -d && npm benchmark
```

### Latest results

```bash
Benchmarking: (4 of 4)
 · globstar-basic
 · negation-basic
 · not-glob-basic
 · star-basic

# benchmark/fixtures/match/globstar-basic.js (182 bytes)
  minimatch x 35,521 ops/sec ±0.99% (82 runs sampled)
  multimatch x 29,662 ops/sec ±1.90% (82 runs sampled)
  nanomatch x 719,866 ops/sec ±1.53% (84 runs sampled)

  fastest is nanomatch

# benchmark/fixtures/match/negation-basic.js (132 bytes)
  minimatch x 65,810 ops/sec ±1.11% (85 runs sampled)
  multimatch x 24,267 ops/sec ±1.40% (85 runs sampled)
  nanomatch x 698,260 ops/sec ±1.42% (84 runs sampled)

  fastest is nanomatch

# benchmark/fixtures/match/not-glob-basic.js (93 bytes)
  minimatch x 91,445 ops/sec ±1.69% (83 runs sampled)
  multimatch x 62,945 ops/sec ±1.20% (84 runs sampled)
  nanomatch x 3,077,100 ops/sec ±1.45% (84 runs sampled)

  fastest is nanomatch

# benchmark/fixtures/match/star-basic.js (93 bytes)
  minimatch x 62,144 ops/sec ±1.67% (85 runs sampled)
  multimatch x 46,133 ops/sec ±1.66% (83 runs sampled)
  nanomatch x 1,039,345 ops/sec ±1.23% (86 runs sampled)

  fastest is nanomatch
```

## History

### key

Changelog entries are classified using the following labels _(from [keep-a-changelog](https://github.com/olivierlacan/keep-a-changelog)_):

* `added`: for new features
* `changed`: for changes in existing functionality
* `deprecated`: for once-stable features removed in upcoming releases
* `removed`: for deprecated features removed in this release
* `fixed`: for any bug fixes
* `bumped`: updated dependencies, only minor or higher will be listed.

### [0.1.0] - 2016-10-08

First release.

## About

### Related projects

* [expand-brackets](https://www.npmjs.com/package/expand-brackets): Expand POSIX bracket expressions (character classes) in glob patterns. | [homepage](https://github.com/jonschlinkert/expand-brackets "Expand POSIX bracket expressions (character classes) in glob patterns.")
* [extglob](https://www.npmjs.com/package/extglob): Convert extended globs to regex-compatible strings. Add (almost) the expressive power of regular expressions to… [more](https://github.com/jonschlinkert/extglob) | [homepage](https://github.com/jonschlinkert/extglob "Convert extended globs to regex-compatible strings. Add (almost) the expressive power of regular expressions to glob patterns.")
* [micromatch](https://www.npmjs.com/package/micromatch): Glob matching for javascript/node.js. A drop-in replacement and faster alternative to minimatch and multimatch. | [homepage](https://github.com/jonschlinkert/micromatch "Glob matching for javascript/node.js. A drop-in replacement and faster alternative to minimatch and multimatch.")

### Contributing

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

Please read the [contributing guide](.github/contributing.md) for avice on opening issues, pull requests, and coding standards.

### Running tests

Install dev dependencies:

```sh
$ npm install -d && npm test
```

### Author

**Jon Schlinkert**

* [github/jonschlinkert](https://github.com/jonschlinkert)
* [twitter/jonschlinkert](http://twitter.com/jonschlinkert)

### License

Copyright © 2016, [Jon Schlinkert](https://github.com/jonschlinkert).
Released under the [MIT license](https://github.com/jonschlinkert/nanomatch/blob/master/LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.2.0, on October 20, 2016._