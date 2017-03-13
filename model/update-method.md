# Model Methods

## UPDATE

This method allows you to update an entity in the Datastore.  
It will first fetch the entity, then update its data with the new ones and finally save the data back to the Datastore

This method accepts the following arguments:

```js
MyModel.update(
    /* {int|string}. -- the id of the entity to update */
    <id>,
    /* {object} -- the data to update */
    <data>,
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

**@Returns** ---&gt; an entity **instance**.

---

Example

```js
const BlogPost = require('./blog-post.model');

const blogPostData = {
    title : 'New title'
};

// Basic
// -----
BlogPost.update(123, blogPostData, function onBlogPostUpdate(err, entity) {
    if (err) {
        // deal with err
    }
    console.log(entity.plain());
});

// Example with *ancestors* and a *namespace*
// ------------------------------------------
BlogPost.update(123, data, ['Grandpa', 123, 'Dad', 123], 'dev.namespace.com', function(err, entity) {
    if (err) {
        // deal with err
    }
    console.log(entity);
});

// The same method can be executed from inside a transaction
var transaction = gstore.transaction();

transaction.run(function(err) {
    if (err) {
        // handle error
        return;
    }

    BlogPost.update(123, data, null, null, transaction);

    transaction.commit(function(err) {
        if (err) {
            // handle error
        }
    });
});
```

**replace** property \(options\)  
The options parameter has a **replace** property \(default to false\) that you can set to true if you want to replace all the entity data. By default, Model.update\(\) does 2 operations inside a **Transaction**:

* first a get\(\) + merges the new data passed in the entity fetched
* then a save\(\)

If just want to override the entity data without doing any merge with the data stored the Datastore, pass replace:true in the options parameter.

```js
BlogPost.update(123, data, null, null, null, {replace:true}, function(err, entity) {
    ...
});
```

If no callback is passed, it will return a Promise

```js
BlogPost.update(123, data).then((data) => {
    const entity = data[0];
    console.log(entity.plain());
});
```



