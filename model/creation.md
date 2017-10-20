# Model

## Creation

Once you have defined your schema, you can create a model calling  
`gstore.model(<EntityKind>, <schema>)`

As you can see, the name of the model will be the entity kind in the Datastore.

Example:

```js
const gstore = require('gstore-node')();
const Schema = gstore.Schema;

const userSchema = new Schema({
    name:{},
    lastname:{},
    email:{}
});

// Create the "User" model
const User = gstore.model('User', userSchema);
```



