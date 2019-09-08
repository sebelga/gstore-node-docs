# SAVE

After you create an entity you can persist its data to the Datastore with `entity.save()`  
This method accepts the following arguments

```javascript
entity.save(
    /* {Transaction} -- optional. Will execute the save operation inside this transaction */
    <transaction>,
    /* {object} -- optional. Additional config */
    <options>
)
```

**@Returns** -- the entity saved

## options

The options argument has a **method** property where you can set the saving method.  
It default to 'upsert'.

```javascript
{
  method: 'upsert|insert|update', // default: 'upsert'
}
```

Example:

```javascript
// blog-post.model.js
const { instances } = require('gstore-node');

const gstore = intances.get('default');

const blogPostSchema = new gstore.Schema({
  title: { type: String },
  createdOn: { type: Date, default: gstore.defaultValues.NOW }
});

module.exports = gstore.model('BlogPost', blogPostSchema);
```

```javascript
// blog-post.controller.js
const BlogPost = require('./blog-post.model');

const data = { title: 'My first blog post' };
const blogPostEntity = new BlogPost(data);

blogPostEntity.save().then((entity) => {
    console.log(entity.entityKey.id); // auto-generated id
}).catch(err => { ... });

// changing the save method
var blogPostEntity = new BlogPost(data);
blogPostEntity.save(null, { method: 'insert' }).then( ... );

// from inside a transaction
// Info: if you have middleware on "pre" see note below
const transaction = gstore.transaction();
transaction.run()
    .then(() => {
        const blogPost = new BlogPost({ title: 'My new blog post' });
        blogPost.save(transaction);

        // ... any other operation on the Transaction

        return transaction.commit();
    })
    .then((response) => {
        // ... transaction finished
        const apiResponse = data[0];
    }).catch((err) => {
       // handle error
    });
```

## Saving inside a Transaction with middleware on Model

If you have ["pre" middlewares](save.md) on the _save_ method of your Model \(`mySchema.pre('save', myMiddleware)`\) you need to **chain the Promise** of the save method before committing the transaction, otherwise the entity **won't be** saved.

You can avoid this by disabling the middlewares on the entity with **preHooksEnabled** set to false on the entity.

Examples:

```javascript
const user = new User({ name: 'john' });
const transaction = gstore.transaction();

// option 1: chaining Promise before committing
transaction.run()
           .then(() => {
               return user.save(transaction) // there are some "pre" save hooks
                           .then(() => {
                               // need to chain Promise before committing
                               return transaction.commit();
                           });
            })
           .then((response) => {
              const apiResponse = data[0];
              // ... transaction finished
            }).catch((err) => {
              // handle error
            });

// option 2: disable "pre" middlewares on entity before saving
transaction.run().then() => {
    User.get(123, null, null, transaction)
        .then((entity) => {
            entity.email = 'john@domain.com';

            // validate before so we can rollback the transaction if necessary
            const valid = user.validate();

            if (!valid) {
                // rollback the transaction;
            }

            // disable pre middleware(s)
            user.preHooksEnabled = false;

            // save inside transaction in "sync"
            user.save(transaction);

            // ... any other transaction operations

        transaction.commit().then(() => {
            ...
        });
    });
});
```

