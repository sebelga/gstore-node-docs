# Model Methods

## key()
Generates one or several entity key(s) for the Model.
This method accepts the following arguments:

```js
MyModel.key(
    /* {int|string}. -- Can also be an Array of ids to generate */
    <id>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>
)
```

Example:
```js
const User = require('./user.model');

// basic
const entityKey = User.key(123);

// with ancestors and namespace
const entityKey = User.key(123, ['Parent', 'keyname'], 'dev.domain.com');

// with an Array of ids
const entityKeys = User.key([123, 456]);
console.log(entityKeys.length); // 2

```