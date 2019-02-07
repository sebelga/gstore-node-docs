# UPDATE

This method allows you to update an entity in the Datastore.  
It will first fetch the entity, then update its data with the new ones and finally save the data back to the Datastore

This method accepts the following arguments:

```javascript
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
    <options>
)
```

**@Returns** -- an entity **instance**.

Example

```javascript
const BlogPost = require('./blog-post.model');

const blogPostData = {
    title : 'New title'
};

BlogPost.update(123, blogPostData).then((entity) => {
    console.log(entity.plain());
});

// with *ancestors* and a *namespace*
BlogPost.update(123, data, ['Grandpa', 123, 'Dad', 123], 'dev.namespace.com')
        .then((entity) => {
            console.log(entity);
        });

// from inside a Transaction
const transaction = gstore.transaction();
transaction.run().then(async () => {
    await BlogPost.update(123, data, null, null, transaction);

    transaction.commit().then(() => { ... });
});
```

## Options

**options** properties

* _dataloader_ \(a Dataloader instance\)
* _replace_ \(Boolean. default: false\)

**dataloader** The **dataloader** instance must be created on _each_ request. [Read the documentation](../../cache-dataloader/dataloader.md) for more information on this.  
The dataloader instance will be added to the entity instance being saved. This means that it is available in your "pre" save middlewares \(attached on the "this" scope \(`const dataloader = this.dataloader;`\)\)

```javascript
const { instances } = require('gstore-node');

const gstore = instances.get('default');

// Important! This should be done on **each** request (read the Dataloader documentation)
const dataloader = gstore.createDataLoader();
BlogPost.update(123, data, null, null, null, { dataloader }).then( ... );
```

**replace** If you set it to _true_ gstore will replace the the entity data in the Datastore without merging the data first. By default, `MyModel.update()` does **2 operations** inside one transaction:

* get\(\) the entity and merges its data with the new data
* save\(\) the entity

If you just want to override the entity data without doing any merge set _replace_ to **true** in the options parameter.

```javascript
BlogPost.update(123, data, null, null, null, { replace:true }).then( ... );

// which is the same as doing

const blogPost = new BlogPost(data, 123);
blogPost.save().then( ... );
```

