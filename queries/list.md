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

// Create Model
const BlogPost = gstore.model('BlogPost', blogPostSchema);
```

**2. Use anywhere**

`MyModel.list(options /*optional*/, callback /*optional*/)`

**@Returns** object with both the **entities** and a **nextPageCursor** for pagination (that can be used in a future call to `MyModel.list({ start: pageCursor }, function(){ ... }`)

Example:
```js
const BlogPost = require('./blog-post.model');

BlogPost.list()
        .then((response) => {
            console.log(response[0].entities);
            console.log(response[0].nextPageCursor); // only present if more results
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

Example with Array of settings (order, select, filters)

```js
const querySettings = {
    order  : [
        { property: 'title' },
        { property: 'createdOn', descending: true }
    ],
    select  : ['title', 'createdOn'],
    filters : [['author', 'John Snow'], ['rating', '>',  4]]
};
```

####Filters
The **value** of a filter can also be a function that **returns** a value. This function will be executed on each request. Usefull for dynamic value for example when retrieving the current date.

Example
```js
const querySettings = {
    filters : ['publishedOn', '<', () => new Date()],
}

...

BlogPost.list().then((response) => {
    // --> will return all BlogPost with a publishedOn date previous from current date.
});
```

####Override settings
The global configuration defined on the Schema can be overridden anytime by passing new settings as first argument. `Model.list(newSettings)`

```js
const newSettings = {
    limit: 20,
    start: 'somPageCursorFromPreviousQuery'
};

BlogPost.list(newSettings)
        .then(function(response) {
            ...
        });
```

**Additional settings** available in override

- namespace {string}
- readAll {boolean} true | false
- format {string} "JSON" (default) | "ENTITY"

Use the **namespace** setting to override the default namespace.

```js
const newSettings = {
    ...
    namespace:'com.prod.myproject',
    readAll: true,
    format: "ENTITY"
};

BlogPost.list(newSettings).then( ... );

```