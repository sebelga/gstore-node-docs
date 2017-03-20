#Queries

## list

Shortcut for listing the entities. For complete control (pagination, start, end...) use the [@google-cloud queries](./google-cloud-queries.md). List queries are meant to quickly list entities with predefined settings.

It supports the following queries parameters

- limit
- order
- select
- ancestors
- filters (default operator is "=" and does not need to be passed)
- start


**1. Define on Schema**

`entitySchema.queries('list', { ...settings });`

Example

```js
// Create Schema
var blogPostSchema = new gstore.Schema({
    title : {type:'string'},
    isDraft: {type: 'boolean'}
});

// List settings
var querySettings = {
    limit    : 10,
    order    : {property: 'title', descending:true}, // descending defaults to false and is optional
    select   : 'title',
    ancestors: ['Parent', 123],  // will add an "hasAncestor" filter
    filters  : ['isDraft', false] // operator defaults to "=",
    start    : 'nextPageCursorFromPreviousQuery' // from a previous query
};

// Add to schema
blogPostSchema.queries('list', querySettings);

// Create Model
var BlogPost = gstore.model('BlogPost', blogPostSchema);
```

**2. Use anywhere**

`Model.list(callback)`
The **response** object in the callback contains both the **entities** and a **nextPageCursor** for pagination (to be used in a next `Model.list({start:pageCursor}, function(){...}` call)

```js
// In your Controllers
BlogPost.list(function(err, response) {
    if (err) {
        // deal with err
    }
    console.log(response.entities);
    console.log(response.nextPageCursor); // only present if more results
});
```

Order, Select & filters can also be **arrays** of settings

```js
var querySettings = {
    orders  : [{property: 'title'}, {property:'createdOn', descending:true}]
    select  : ['title', 'createdOn'],
    filters : [['title', 'My first post'], ['createdOn', '<',  new Date()]]
};
```

The **value** of a filter can also be a function that **returns** a value. This function will be executed on on each request. Usefull for dynamic value like retrieving the current date.

```js
var querySettings = {
	filters : ['publishOn', '<', () => new Date()],
	...
}

// In a Controller request
BlogPost.list(function(err, response) {
	// --> will return all BlogPost with a publishOn property date previous of today's date.
});
```

**Override settings**  
These global settings defined on the schema can be overridden anytime by passing new settings as first parameter. `Model.list(settings, cb)`

```js
var newSettings = {
    limit : 20,
    start : 'pageCursor'
};

BlogPost.list(newSettings, function(err, entities) {
    if (err) {
        // deal with err
    }
    console.log(entities);
});
```

**Additional settings** in override

- namespace {string}
- readAll {boolean} true | false
- format {string} gstore.Queries.formats.JSON (default) | gstore.Queries.formats.ENTITY

Use the **namespace** setting to override the default namespace.

```js
var newSettings = {
    ...
    namespace:'com.domain-dev',
    readAll: true,
    format: gstore.Queries.formats.ENTITY
};

BlogPost.list(newSettings, ...);
```

If no callback is passed, a **Promise** is returned

```js
BlogPost.list(/*settings*/).then((data) => {
	const entities = data[0];
    console.log(entities);
});
```