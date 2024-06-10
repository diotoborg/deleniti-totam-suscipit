# opq

[![build](https://github.com/diotoborg/deleniti-totam-suscipit/actions/workflows/build.yml/badge.svg?branch=main)](https://github.com/diotoborg/deleniti-totam-suscipit/actions/workflows/build.yml)
[![install size](https://packagephobia.com/badge?p=@diotoborg/deleniti-totam-suscipit)](https://packagephobia.com/result?p=@diotoborg/deleniti-totam-suscipit)

Node.js client library for constructing OpenSearch query.

- [opq](#opq)
  - [Installation](#installation)
  - [Quick Start](#quick-start)
  - [Query](#query)
    - [matchPrefix](#matchprefix)
    - [matchBool](#matchbool)
    - [match](#match)
    - [multimatch](#multimatch)
    - [matchAll](#matchall)
    - [term](#term)
    - [terms](#terms)
    - [exists](#exists)
    - [range](#range)
    - [withShould](#withshould)
    - [withMust](#withmust)
    - [withMustNot](#withmustnot)
    - [withBool](#withbool)
    - [withQuery](#withquery)
    - [withFilter](#withfilter)
    - [withPaginate](#withpaginate)
    - [withHighlight](#withhighlight)
    - [withSort](#withsort)
    - [withSource](#withsource)
    - [withConstant](#withconstant)
    - [withArray](#witharray)
    - [withSiblings](#withsiblings)
    - [withPrettyPrint](#withprettyprint)
    - [withScriptScore](#withscriptscore)
  - [Pipeline](#pipeline)
  - [Client](#client)
  - [Debugging](#debugging)


## Installation

```sh
npm install @diotoborg/deleniti-totam-suscipit
```

## Quick Start

Constructing queries for OpenSearch index has never been this simplified. opq provides queries that can be used to construct simple, complex and custom JSON queries for OpenSearch.

See example: [match.js](./examples/match.js)

```js
const { pipeline, query } = require('@diotoborg/deleniti-totam-suscipit');
const pipelineQ = pipeline(
    query.match('customer_first_name', 'Sonya'),
    query.withQuery(),
);

console.log(pipelineQ());
```

```sh
$ node match.js
{ query: { match: { customer_first_name: [Object] } } }
```

Here's a more complete query based on [Paginate results documentation](https://opensearch.org/docs/latest/search-plugins/searching-data/paginate/#the-search_after-parameter) from OpenSeach using several query from opq and pretty printing the output.

See example: [paginate.js](./examples/paginate.js)

```js
const { pipeline, query } = require('@diotoborg/deleniti-totam-suscipit');
const paginateQ = pipeline(
    query.match('play_name', 'Hamlet'),
    query.withPaginate(0, 3),
    query.withSort([
        {
            'speech_number': 'asc'
        },
        {
            '_id': 'asc'
        }
    ]),
    query.withQuery(),
    query.withPrettyPrint(),
);

paginateQ();

```

```sh
$ node paginate.js

{
    "query": {
        "match": {
            "play_name": {
                "query": "Hamlet"
            }
        },
        "from": 0,
        "size": 3,
        "sort": [
            {
                "speech_number": "asc"
            },
            {
                "_id": "asc"
            }
        ]
    }
}
```

More snippets can be found in the [examples directory](./examples)

## Query

Opq exports `query.*` which has several functions that can be used within `pipeline`.

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');
```

### matchPrefix

This allows including the `match_phrase_prefix` field which searches for documents matching the provided phrase.

See example: [matchprefix.js](./examples/matchprefix.js)

### matchBool

This allows including the `match_bool_prefix` field which analyzes the provided search string and creates a Boolean query.

See example: [matchbool.js](./examples/matchbool.js)

### match

This allows including the `match` field to perform full-text search.

See example: [match.js](./examples/match.js)

### multimatch

This allows including the `multimatch` field to search multiple fields in the index. By default, `fields` will be `*` which searches the fields specified in the index query.

See example: [multimatch.js](./examples/multimatch.js)

### matchAll

This allows including the `match_all` field that queries the entire index.

### term

This allows including the `term` field.

See example: [array.js#L9](./examples/array.js#L9)

### terms

This allows including the `terms` field.

See example: [array.js#L10](./examples/array.js#L10)

### exists

checks if a field with a name exists.

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');

const withExists = query.exists({
    'name': 'created_at',
});

```

### range

This allows to search for a range of given values in a field.

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');

const withRange = query.range('created_at', {
    'gte': '2023-04-01',
    'lte': '2024-04-08'
});
```

### withShould

This allows include the `should` field which is equivalent to logical `or` operator.

See example: [match.js#L9](./examples/match.js#L9)

### withMust

This allows include the `must` field which is equivalent to logical `and` operator.

See example: [siblings.js#L10](./examples/siblings.js#L10)

### withMustNot

This allows include the `must_not` field which is equivalent to logical `not` operator.

See example: [array.js#L19](./examples/array.js#L19)

### withBool

This allows including the `bool` field that can be combined with `withMustNot`, `withMust`, `withShould` and `withFilter`.

See example: [siblings.js#L11](./examples/siblings.js#L11)

### withQuery

This allows including the `query` field.

See example: [array.js#L23](./examples/array.js#L23)

### withFilter

This allows including the `filter` field.

See example: [filter.js](./examples/filter.js)

### withPaginate

This allows including the `from` and `size` fields. The `from` field is computed based on `offset` and `limit` provided.

See example: [paginate.js](./examples/paginate.js)

### withHighlight

This allows including the `highlight` field with option to replace the `preTags`, `postTags` and [additional highlighting options](https://opensearch.org/docs/latest/search-plugins/searching-data/highlight/#highlighting-options).

See example: [highlight.js](./examples/highlight.js)

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');

query.withHighlight([
    { 'email': {} },
    { 'username': {} }
], {
    'pre_tags': ['<em>'],
    'post_tags': ['</em>'],
    'order': 'score',
    'number_of_fragments': 0,
});
```

### withSort

This allows including the `sort` field with option to add [flexible attributes](https://opensearch.org/docs/latest/search-plugins/searching-data/sort/).

See example: [paginate.js#L8](./examples/paginate.js#L8)

### withSource

This allows including the `_source` field.

See example: [array.js#L24](./examples/array.js#L24)

### withConstant

This allows providing constant value with custom key.

See example: [siblings.js#L8](./examples/siblings.js#L8)

### withArray

This allows placing sub-queries within an array.

See example: [array.js](./examples/array.js)

### withSiblings

This allows placing adjacent queries or sub-queries.

See example: [siblings.js](./examples/siblings.js)

### withPrettyPrint

By default, prettyprint outputs composed query to console. You may change the default logger.

See example: [paginate.js#L17](./examples/paginate.js#L17)

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');
const winston = require('winston');
const logger = winston.createLogger({
    transports: [
        new winston.transports.Console({ level: 'warn' }),
        new winston.transports.File({ filename: 'combined.log' })
    ],
});

query.withPrettyPrint({}, logger.info);
```

### withScriptScore

This allows including the `script_score` to change the scoring function of queried documents.

```js
const { query } = require('@diotoborg/deleniti-totam-suscipit');

query.withScriptScore(
    query.match('author', 'Dave')(),
    {
        source: `
            _score * doc[params.field].value
        `,
        params: {
            'field': 'multiplier'
        }
    }
);

```

```sh
{
    'script_score': {
        'query': {
            'match': {
                'author': {
                    'query': 'Dave',
                    . . .
                }
            }
        },
        'script': {
            lang: 'painless',
            source: '\n         _score * doc[params.field].value\n         ',
            params: { 'field': 'multiplier' }
        }
    }
}
```


## Pipeline

You can execute custom queries in addition to the built-in queries using `pipeline`.

See example: [custom.js](./examples/custom.js)

```js
const { pipeline } = require('@diotoborg/deleniti-totam-suscipit');

const customFunction = (text) => {
    return (baseQuery) => ({
        ...baseQuery,
        'custom_attribute': text
    });
};

const pipelineQ = pipeline(
    query.match('fruit_name', 'Orange'),
    query.withQuery(),
    customFunction('custom_value'),
    query.withPrettyPrint(),
);

pipelineQ();
```

```sh
$ node sample-custom.js

{
    "query": {
        "match": {
            "fruit_name": {
                "query": "Orange",
                "operator": "OR",
                "max_expansions": 30,
                "boost": 1
            }
        }
    },
    "custom_attribute": "custom_value"
}
```


## Client

Connection to OpenSearch can be initiated using `newClient`. It requires at minimum `host`, `username` and `password` but you can provide additional configuration to fine-tune the performance such as circuit breaking.

See example: [client.js](./examples/client.js)

```js
const { newClient } = require('@diotoborg/deleniti-totam-suscipit');
const client = newClient({
    // required
    host: 'localhost:9200',
    username: 'admin',
    password: 'admin',

    // optional
    enableLongNumeralSupport: true,
    memoryCircuitBreakerEnabled: true,
    memoryCircuitBreakerMaxPercent: 0.8,
});
```

You can validate configuration credentials using `createCredentials`.

```js
const { createCredentials } = require('@diotoborg/deleniti-totam-suscipit');

console.log(createCredentials({
    // required
    host: 'localhost:9200',
    username: 'admin',
    password: 'admin',

    // optional
    protocol: 'http' // defaults to https
}));
```

## Debugging

[debug](https://www.npmjs.com/package/debug) a tiny JavaScript debugging utility tool is used for client error and info output. The debug output can be toggled by prefix node command with `DEBUG=*` for the whole module or `DEBUG=opensearch:opq` for opq only output.

```sh
DEBUG=* node examples/client.js
```
