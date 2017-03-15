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