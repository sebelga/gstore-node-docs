#Queries

## findOne

Quickly find an entity by passing key/value pairs. You can optionally pass an ancestors array or a namespace.

**@Returns** an gstore entity **instance** of the Model.

This method accepts the following arguments:

```js
MyModel.findOne(
    /* {object}. -- Key/Value pairs to look for */
    <propsValues>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

Example:
```js
const User = require('./user.model');

User.findOne({ email: 'john@snow.com' })
    .then((entity) => {
        console.log(entity.plain()); // entityData + id
        console.log(entity.firstname)); // 'John'
});


// with a callback
User.findOne({ email: 'john@snow.com' }, function onEntity(err, entity) {
    if (err) {
        ... // deal with error
    }

    console.log(entity.plain());
    console.log(entity.get('name'));
});

```