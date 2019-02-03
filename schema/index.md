# About

You define your Datastore entities models with **Schemas**. This is where you set the properties of an entity, its types, default values, read/write value...

It is also on schemas that you can add **pre** and **post** middlewares for different operations on your entities. For example before saving, or after deleting and entity.

## Define a Model Schema

Let's define a simple "User" Model schema.

```javascript
const gstore = require('gstore-node')();
const Schema = gstore.Schema;

const userSchema = new Schema({
    name: {},
    lastname: {},
    password: {},
    email: {},
    votes: {},
    dateOfBirth: {},
    createdOn: {},
    modifiedOn: {}
});
```

With this simple schema, if you try to save an entity with any other property than the ones defined here, it won't validate and won't be saved in the Datastore.  
Guaranteed! :\)

Let's [add now some type](type_validation.md) to those properties.

