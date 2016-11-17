# Schema

You define your Datastore entities models with **Schemas**. This is where you set the allowed properties for an entity, their types, default values, read/write...

It is also on the schemas that you can add **pre** and **post** middelware for different operations on your entities. For example before saving, or after deleting and entity. 

## Define a Model Schema

Let's define a simple "User" Model schema.

```js
const gstore = require('gstore-node');
const Schema = gstore.Schema;

const userSchema = new Schema({
    name: {},
    lastname: {},
    password: {},
    email: {},
    dateOfBirth: {}
});
```

With this simple schema, if you try to save an entity another property than "name", "lastname" or "email", it won't validate and won't be saved in the Datastore.
Guaranteed! :)

Let's add some 
