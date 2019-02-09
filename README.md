# Text-Search parser for PostgreSQL

[![npm version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![coverage status][codecov-image]][codecov-url]

### Why?

Using pg's `to_tsquery` directly with user input can throw errors. `plainto_tsquery` sanitizes the user input, but it's very limited (it just puts an and between words), similarly `websearch_to_tsquery` does this, except between quotes.

This module allows customizable text-search operators (and, or, followedBy, not, prefix, parentheses, quoted text), while also preserving quoted text just like `websearch_to_tsquery`.

See the [full options](index.js#L2-L12) and [defaults](index.js#L16-L27)

### Usage
```js
const tsquery = require('pg-tsquery')(/* options can be passed to override the defaults */);

pool.query('SELECT * FROM tabby WHERE to_tsvector(col) @@ to_tsquery($1)', [tsquery(str)])
```

| inputs | output |
| --- | --- |
| `foo bar` | `foo&bar` |
| `foo -bar`, `foo !bar`, `foo + !bar` | `foo&!bar` |
| `foo bar,bip`, `foo+bar \| bip` | `foo&bar\|bip` |
| `foo (bar,bip)`, `foo+(bar\|bip)` | `foo&(bar\|bip)` |
| `foo>bar>bip` | `foo<->bar<->bip` |
| `foo*,bar* bana:*` | `foo:*\|bar:*&bana:*` |


### [Demo](https://caub.github.io/pg-tsquery)

Supports Nodejs>=10. For lower versions, you might need `if (!String.prototype.trimStart) String.prototype.trimStart = String.prototype.trim;`

[npm-image]: https://img.shields.io/npm/v/pg-tsquery.svg?style=flat-square
[npm-url]: https://www.npmjs.com/package/pg-tsquery
[travis-image]: https://img.shields.io/travis/caub/pg-tsquery.svg?style=flat-square
[travis-url]: https://travis-ci.org/caub/pg-tsquery
[codecov-image]: https://img.shields.io/codecov/c/github/caub/pg-tsquery.svg?style=flat-square
[codecov-url]: https://codecov.io/gh/caub/pg-tsquery
