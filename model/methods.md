# Model Methods

## GET

Retrieving an entity by key is the fastest way to read from the Datastore.  
This method accepts the following parameters:

* id {int, string} \(can also be an **array** of ids to retrieve\)
* ancestors {Array} \(optional\)
* namespace \(optional\)
* transaction \(optional\)
* options \(optional\)
* callback \(optional, if not passed a **Promise** is returned\)

Returns ---&gt; an entity **instance**.

```js
var blogPostSchema = new gstore.Schema({...});
var BlogPost       = gstore.model('BlogPost', blogPostSchema);

// id can be integer or string
BlogPost.get(1234, function(err, entity) {
    if (err) {
        // deal with err
    }
    console.log('Blogpost title:', entity.get('title'));
});

// Passing an ancestor path
BlogPost.get('keyname', ['Parent', 'parentName'], function(err, entity) {
    if (err) { // deal with err }
    ...
});
```

The resulting entity has a **plain\(\)** method \([see below](#entityPlain)\) attached to it that returns only the entity data and its id.

```js
BlogPost.get(123, function(err, entity) {
    if (err) { // deal with err }
    console.log(entity.plain());
});
```

If you need to retrieve an entity from inside a transaction, pass it as fourth parameter.

```js
var error;

var transaction = gstore.transaction();

transaction.run(function(err) {
    if (err) {
        // handle error
        return;
    }

    BlogPost.get(123, null, null, transaction, function(err, entity) {
        if (err) {... deal with error }

        // entity is an instance of the BlogPost model with all its properties & methods

        transaction.commit(function(err) {
            if (err) {
                // transaction will be automatically rolled back on failure
                // handle error
            }
        });
    });
});
```

If no callback is passed, it will return a Promise

```js
BlogPost.get(123).then((data) => {
    const entity = data[0];
    console.log(entity.plain());
});
```

**options** parameter  
The options object parameter has a **preserveOrder** property \(default to false\). Useful when an array of IDs is passed and you want to preserve the order of those ids in the results.

```js
BlogPost.get([1,2,3], null, null, null, {preserveOrder:true}, function(err, entities) {
    if (err) { // deal with err }

    // Order is preserved
    console.log(entities[0].entityKey.id); // 1
    console.log(entities[1].entityKey.id); // 2
    console.log(entities[2].entityKey.id); // 3
});
```

**Note**: setting this property to true does take some processing, especially for large sets. Only use it if you absolutely need to maintain the original order passed.

