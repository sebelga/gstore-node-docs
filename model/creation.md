# Creation

Once you have defined your schema, you can create a model calling  
`gstore.model(<EntityKind>, <schema>)`

As you can see, the name of the model will be the entity kind in the Datastore.

Example:

```javascript
const { instances } = require('gstore-node');

const gstore = instances.get('default');
const Schema = gstore.Schema;

const userSchema = new Schema({
    name:{},
    lastname:{},
    email:{}
});

// Create the "User" model
const User = gstore.model('User', userSchema);
```

