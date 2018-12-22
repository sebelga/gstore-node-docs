# Queries

## list()

Shortcut for listing entities from a Model. For complete control (pagination, start, end...) use the [@google-cloud queries](./google-cloud-queries.md). When you configure a `list()` shortcut query on a Schema, you can then later quickly fetch entities with pre-configured settings, like the _order by_ or _limit_ (those settings can be overridden later on).

### 1. Configure "list" query options on the Schema

`entitySchema.queries('list', options);`

#### options

The options object accepts the following "queries" properties

- limit
- order
- select
- ancestors
- filters (default operator is "=" and does not need to be passed)
- start
- offset

**INFO**: "order", "select" & "filters" can also be **Arrays** of settings (see example below).


**Additional configuration**
The options accepts also the following properties

- namespace {string}
- readAll {boolean} true | false
- format {string} "JSON" | "ENTITY" (default: "JSON")
- showKey {boolean} true | false
- cache {boolean} true | false
- ttl {number | object}
- consistency {'strong' | 'eventual'}

**namespace**
Overrides the default gstore instance namespace.

**showKey** (default: false)

Adds a "__key" property to the entity data with the complete Key information from the Datastore.

**cache** (default: the "global" cache configuration)    
"true" = read from the cache and prime the cache with the query response.  

**ttl** (default: the `cache.ttl.queries` value)
Custom TTL value for the cache. For multi-store it can be an object of TTL values.

```js
const options = {
    ...
    namespace:'com.prod.myproject',
    readAll: true,
    format: "ENTITY",
    showKey: true,
    ttl: { memory: 60, redis: 600 }
};
```

**consistency**
Specify either "strong" or "eventual". If not specified, default values are chosen by Datastore for the operation. Learn more about strong and eventual consistency [here](https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore).



Example

```js
// blog-post.model.js

// Create Schema
const blogPostSchema = new gstore.Schema({
    title : { type: String },
    isDraft: { type: Boolean }
});

// List query settings
const listQueryOptions = {
    limit : 10,
    order : { property: 'title', descending: true }, // descending defaults to false and is optional
    select : 'title',
    ancestors : ['Parent', 123],  // will add an "hasAncestor" filter
    filters : ['isDraft', false] // operator defaults to "=" and is optional,
};

// Configure "list" shortcut Query
blogPostSchema.queries('list', listQueryOptions);

module.exports = gstore.model('BlogPost', blogPostSchema);
```

### 2. Use anywhere

`MyModel.list(options /*optional*/, callback /*optional*/)`

**@Returns**: the response is an object with 2 properties:
- entities
- nextPageCursor // only present if there are more results to fetch

The **nextPageCursor** is for pagination and can be used in a future call to `MyModel.list({ start: pageCursor })`

Example:
```js
const BlogPost = require('./blog-post.model');

BlogPost.list()
    .then((response) => {
        console.log(response.entities);
        console.log(response.nextPageCursor); // only present if there are more results
    });

// with a callback
BlogPost.list(function(err, response) {
    if (err) {
        // deal with err
    }
    console.log(response.entities);
    console.log(response.nextPageCursor);
});
```

Example with Arrays of values (order, select, filters)

```js
const listQueryOptions = {
    order  : [
        { property: 'title' },
        { property: 'createdOn', descending: true }
    ],
    select  : ['title', 'createdOn'],
    filters : [['author', 'John Snow'], ['rating', '>',  4]]
};

blogPostSchema.queries('list', listQueryOptions);
```

####Filters

The **value** of a filter can also be a **function that returns a value**. This function will be executed on each request. Useful for dynamic value for example when retrieving the current date.

Example
```js
const listQuerySettings = {
    filters : ['publishedOn', '<', () => new Date()],
};

...

BlogPost.list().then((response) => {
    // --> will return all BlogPost with a publishedOn date previous from current date.
});
```

####Override settings

The global configuration set on the Schema can be overridden anytime by passing a new options object. `Model.list(options)`

```js
const options = {
    limit: 20,
    start: 'somPageCursorFromPreviousQuery'
};

BlogPost.list(options)
    .then((response) => { ... });
```

