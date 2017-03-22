# Model Methods

## get()

Retrieving an entity **by key** is the fastest way to read from the Datastore.  
This method accepts the following arguments:

```js
MyModel.get(
    /* {int|string}. -- Can also be an Array of ids to retrieve */
    <id>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {Transaction} -- optional. The transaction currently in progress */
    <transaction>,
    /* {object} -- optional. Additional config */
    <options>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

**@Returns** ---> a gstore entity **instance**.

---

Example:

```js
const BlogPost = require('./blog-post.model');

// id can be integer or string
BlogPost.get(123).then((entity) => {
    console.log(entity.plain());
});

// Passing an ancestor path with a string id
const ancestors = ['Parent', 'parentName'];
BlogPost.get('stringId', ancestors).then((entity) => { ... });

// ... with a callback
BlogPost.get(1234, function onEntity(err, entity) {
    if (err) { // deal with err }
    console.log('Blogpost title:', entity.title);
});
```

The resulting entity has a **plain\(\)** method that returns an object with the entity **data ** + its **id**.

```js
BlogPost.get(123, function(err, entity) {
    if (err) { // deal with err }
    console.log(entity.plain());
});
```

If you need to retrieve an entity **from inside a transaction**, you can pass the transaction object as fourth parameter.

```js
const transaction = gstore.transaction();

transaction.run(function onTransaction(err) {
    if (err) {
        // handle error
        return;
    }

    BlogPost.get(123, null, null, transaction, function onEntity(err, entity) {
        if (err) {... deal with error }

        // Info: entity is an instance of the BlogPost model with all its properties & methods

        transaction.commit(function transactionCommited(err) {
            if (err) {
                // transaction will be automatically rolled back on failure
                // handle error
            }
            ...
        });
    });
});
```

If no callback is passed, the get\(\) method will return a **Promise**

```js
BlogPost.get(123).then((data) => {
    const entity = data[0];
    console.log(entity.plain());
});
```

**options** argument

  - preserveOrder (default: false)
    
The **preserveOrder** option property is useful when you pass an array of IDs to retrieve and you want to preserve the order of those ids in the response.

**Note**: setting this property to true does add some processing, especially for large sets. Only use it if you absolutely need to maintain the original order passed.

```js
BlogPost.get([1,2,3], null, null, null, {preserveOrder:true}, function(err, entities) {
    if (err) { // deal with err }

    // Order is preserved
    console.log(entities[0].entityKey.id); // 1
    console.log(entities[1].entityKey.id); // 2
    console.log(entities[2].entityKey.id); // 3
});
```



