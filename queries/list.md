#Queries

## list

Shortcut for listing entities from a Model. For complete control (pagination, start, end...) use the [@google-cloud queries](./google-cloud-queries.md). **List queries** are meant to quickly list entities with predefined settings (that can be overridden on a query).

It supports the following queries parameters

- limit
- order
- select
- ancestors
- filters (default operator is "=" and does not need to be passed)
- start
- offset

**INFO**: "order", "select" & "filters" can also be **Arrays** of settings

**1. Configure List query settings on your Model Schema**

`entitySchema.queries('list', { ...settings });`

Example

```js
// blog-post.model.js

// Create Schema
const blogPostSchema = new gstore.Schema({
    title : { type: 'string' },
    isDraft: { type: 'boolean' }
});

// List query settings
const listQuerySettings = {
    limit : 10,
    order : { property: 'title', descending: true }, // descending defaults to false and is optional
    select : 'title',
    ancestors : ['Parent', 123],  // will add an "hasAncestor" filter
    filters : ['isDraft', false] // operator defaults to "=",
};

// Add settings to schema
blogPostSchema.queries('list', listQuerySettings);

module.exports = gstore.model('BlogPost', blogPostSchema);
```

**2. Use anywhere**

`MyModel.list(options /*optional*/, callback /*optional*/)`

**@Returns**: the response is an object with 2 properties:
- entities
- nextPageCursor // only present if there are more results to fetch

The **nextPageCursor** is for pagination and can be used in a future call to `MyModel.list({ start: pageCursor })`)

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

Example with an Array of settings (order, select, filters)

```js
const listQuerySettings= {
    order  : [
        { property: 'title' },
        { property: 'createdOn', descending: true }
    ],
    select  : ['title', 'createdOn'],
    filters : [['author', 'John Snow'], ['rating', '>',  4]]
};

blogPostSchema.queries('list', listQuerySettings);
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

The global configuration defined on the Schema can be overridden anytime by passing new settings as options argument. `Model.list(options)`

```js
const options = {
    limit: 20,
    start: 'somPageCursorFromPreviousQuery'
};

BlogPost.list(options)
        .then(function(response) {
            ...
        });
```

**Additional options parameters**

- namespace {string}
- readAll {boolean} true | false
- format {string} "JSON" (default) | "ENTITY"
- showKey {boolean} true | false
- cache {boolean} true | false
- ttl {number | object}

**namespace**
Overrides the default namespace.

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

BlogPost.list(options).then( ... );

```