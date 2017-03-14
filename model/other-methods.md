# Model Methods

## excludeFromIndexes()

When you create a Schemaless Models (with **explicityOnly ** set to false on your Schema), all the properties not declared explicitly will automatically be added to your indexes in the Datastore.

If you don't want this behaviour you can call `Model.excludeFromIndexes(property)` passing a **string** property or an **Array** of properties. If one of the property passed is already declared on the Schema, this method will set its excludeFromIndexes value to false.

Example:

```js
// Your model declaration

const options = { explicitOnly: false };
const blogPostSchema = new Schema({
    title: { type: 'string' }
}, options);

BlogPost = gstore.model('BlogPost', blogPostSchema);

...

// Yout controller

const blogPost = new BlogPost({
    title:'my-title',
    text:`some very long text that can not be indexed because of the limitation
            of the Datastore where the max length is 1,500 bytes for text properties.
            It takes 1 to 6 bytes for 1 UTF-8 char ~= 500 characters`
    });

/*
If you save this entity, the text won't be saved in the Datastore
because of the size limitation of the indexes (can't be more that 1500 bytes).

https://cloud.google.com/datastore/docs/concepts/limits

Assuming that the "text" propery is dynamic and is not known at Schema instantiation,
you need to remove the propery from indexes before saving.
*/

BlogPost.excludeFromIndexes('text');

// now you can save the entity
blogPost.save().then(() => { ... });

```

## sanitize()

This methods will clean and do basic formatting of an entity data. It is a good practice to call it on data coming from an untrusted source.  Sanitize() will:

- **remove properties** that are marked as **not writable** in schemas
- convert 'null' (string) values to **null**

```js
// user.model.js

const userSchema = new Schema({
    name : { type: 'string' },
    createdOn : { type: 'datetime', write:false } // write not allowed
});

module.exports = gstore.model('User', userSchema);

...
// In your controller
const User = require('./user.model');

const data = req.body; // body of the Request
console.log(data.createdOn); // '2016-03-01T21:30:00';
console.log(data.lastname); // "null";

data = User.sanitize(data);
console.log(data.createdOn); // undefined
console.log(data.lastname); // null
```

## key()
Creates and entity key(s) for the Model. This method accepts the following arguments:

- id (one or several in an Array)
- ancestors (optional)
- namespace (optional)

```js
var User = gstore.model('User');

var entityKey = User.key(123);

// with ancestors and namespace
var entityKey = User.key(123, ['Parent', 'keyname'], 'dev.domain.com');

// passing array of ids

var entityKeys = User.key([123, 456]);
console.log(entityKeys.length); // 2

```