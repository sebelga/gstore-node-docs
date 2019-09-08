# deleteAll\(\)

If you need to delete all the entities of a Model, this shortcut query will do just that. It queries the entitites by batches of 500 \(maximum set by the Datastore\) and delete them then repeat the query.

Disclaimer: this shortcut query hasn't been tested on a very large set of entities so please report any issue you might find.

This method accepts the following arguments:

```javascript
MyModel.deleteAll(
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>
)
```

**@Returns** -- an object with a **success**  and a **message** properties.

Example:

```javascript
BlogPost.deleteAll().then((response) => {
    console.log(response.success);
});


// With ancestors path and namespace
BlogPost.deleteAll(['Grandpa', 1234, 'Dad', 'keyname'], 'com.dev.new-domain')
        .then((response) => {
            ...
        });
```

