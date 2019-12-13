# key\(\)

Generates one or several entity key\(s\) for the Model. This method accepts the following arguments \(all arguments are optional\):

```javascript
MyModel.key(
    /*
     {string | { id: string|number } | { name: string }}
     It can also be an Array of ids to generate
     */
    <id>,
    /* {Array} -- optional. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>
)
```

Example:

```javascript
const User = require('./user.model');

// Providing a Key "id"
const entityKey = User.key({ id: 123 });
// "string" id will automatically be converted to integer
const entityKey = User.key({ id: '123' });

// You can also provide a key "name"
const entityKey = User.key({ name: 'myEntity' });
// or passing the string name directly
const entityKey = User.key('myEntity');

// with ancestors and namespace
const entityKey = User.key({ id: 123 }, ['Parent', 'keyname'], 'dev.domain.com');

// with an Array of ids
const entityKeys = User.key([{ id: '123' }, 'myEntity']);
console.log(entityKeys.length); // 2
```

