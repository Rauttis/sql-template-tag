# SQL Template Tag

[![NPM version][npm-image]][npm-url]
[![NPM downloads][downloads-image]][downloads-url]
[![Build status][build-image]][build-url]
[![Build coverage][coverage-image]][coverage-url]

> ES2015 tagged template string for preparing SQL statements.

## Installation

```
npm install sql-template-tag --save
```

## Usage

```ts
import sql, { empty, join, raw } from "sql-template-tag";

const query = sql`SELECT * FROM books WHERE id = ${id}`;

query.sql; //=> "SELECT * FROM books WHERE id = ?"
query.text; //=> "SELECT * FROM books WHERE id = $1"
query.values; //=> [id]

pg.query(query); // Uses `text` and `values`.
mysql.query(query); // Uses `sql` and `values`.

// Embed SQL instances inside SQL instances.
const nested = sql`SELECT id FROM authors WHERE name = ${"Blake"}`;
const query = sql`SELECT * FROM books WHERE author_id IN (${nested})`;

// Join and "empty" helpers (useful for nested queries).
sql`SELECT * FROM books ${hasIds ? sql`WHERE ids IN (${join(ids)})` : empty}`;
```

### Join

Accepts an array of values and returns a SQL instance with the values joined by the separator. E.g.

```js
const query = join([1, 2, 3]);

query.sql; //=> "?, ?, ?"
query.values; //=> [1, 2, 3]
```

### Raw

Accepts a string and returns a SQL instance, useful if you want some part of the SQL to be dynamic.

```js
raw("SELECT"); // == sql`SELECT`
```

**Do not** accept user input to `raw`, this will create a SQL injection vulnerability.

### Empty

Simple placeholder value for an empty SQL string. Equivalent to `raw("")`.

## Recipes

This package "just works" with [`pg`](https://www.npmjs.com/package/pg) and [`mysql`](https://www.npmjs.com/package/mysql).

### [MSSQL](https://www.npmjs.com/package/mssql)

```js
mssql.query(query.strings, ...query.values);
```

## Related

Some other modules exist that do something similar:

- [`sql-template-strings`](https://github.com/felixfbecker/node-sql-template-strings): promotes mutation via chained methods and lacks nesting SQL statements. The idea to support `sql` and `text` properties for dual `mysql` and `pg` compatibility came from here.
- [`pg-template-tag`](https://github.com/XeCycle/pg-template-tag): missing TypeScript and MySQL support. This is the API I envisioned before writing this library, and by supporting `pg` only it has the ability to [dedupe `values`](https://github.com/XeCycle/pg-template-tag/issues/5#issuecomment-386875336).

## License

MIT

[npm-image]: https://img.shields.io/npm/v/sql-template-tag
[npm-url]: https://npmjs.org/package/sql-template-tag
[downloads-image]: https://img.shields.io/npm/dm/sql-template-tag
[downloads-url]: https://npmjs.org/package/sql-template-tag
[build-image]: https://img.shields.io/github/workflow/status/blakeembrey/sql-template-tag/CI/main
[build-url]: https://github.com/blakeembrey/sql-template-tag/actions/workflows/ci.yml?query=branch%3Amain
[coverage-image]: https://img.shields.io/codecov/c/gh/blakeembrey/sql-template-tag
[coverage-url]: https://codecov.io/gh/blakeembrey/sql-template-tag
