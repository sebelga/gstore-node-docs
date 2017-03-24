#Queries

## deleteAll

If you need to delete all the entities of a Model, this shortcut query will do just that.
It queries the entitites by batches of 500 (maximum set by the Datastore) and delete them then repeat the query.

Disclaimer: this shortcut query hasn't been tested on a very large set of entities so please report any issue you might find.

This method accepts the following arguments:

```js
MyModel.deleteAll(
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

**@Returns** -- an object with a **success ** and a **message** properties.

Example:
```js
BlogPost.deleteAll().then((response) => {
    console.log(response.success);
});


// With ancestors path and namespace
BlogPost.deleteAll(['Grandpa', 1234, 'Dad', 'keyname'], 'com.dev.new-domain')
        .then((response) => {
            ...
        });

// with a callback
BlogPost.deleteAll(function(err, response){
    if (err) {// deal with err}

    console.log(response.message);
});
```

