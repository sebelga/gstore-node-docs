# Model Methods

## update()

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

// Basic example
// --------------
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

// Example from inside a Transaction
// ---------------------------------

const transaction = gstore.transaction();

transaction.run().then(() => 
    BlogPost.update(123, data, null, null, transaction);

    transaction.commit(function(err) {
        if (err) {
            // handle error
        }
    });
});
```


### Options

**replace** (default: false)
The options argument has a *replace* property that you can set to true if you want to replace all the entity data without merging with the data in the Datastore. By default, `MyModel.update(...)` does 2 operations inside one transaction:

* get() the entity + merges its data with the ones passed
* save()

If you just want to override the entity data without doing any merge set *replace* to **true** in the options parameter.

```js
BlogPost.update(123, data, null, null, null, { replace:true }).then( ... );
```

If no callback is passed, it will return a **Promise**

```js
BlogPost.update(123, data).then((data) => {
    const entity = data[0];
    console.log(entity.plain());
});
```


