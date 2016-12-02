# Value validation

gstore uses the great validator library (https://github.com/chriso/validator.js) to validate input values so you can use any of the validations from that library.

```js
var entitySchema = new Schema({
    email  : {validate: 'isEmail'},
    website: {validate: 'isURL'},
    color  : {validate: 'isHexColor'},
    ...
});
```

You can also define an **Array of valid values** for a properties.  
If you then try to save an entity with a different value it won't validate and won't be saved in the Datastore.

```js
var entitySchema = new Schema({
    color  : {values: ['#ffffff', '#ff6000', '#000000'},
    ...
});
```