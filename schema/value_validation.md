# Schema
## Value validation

gstore uses the great validator library \([https://github.com/chriso/validator.js](https://github.com/chriso/validator.js)\) to validate input values so you can use any of the validations from that library.

```js
const entitySchema = new Schema({
    email  : { validate: 'isEmail' },
    website: { validate: 'isURL '},
    color  : { validate: 'isHexColor' },
    ...
});
```

If you need to use a function that requires extra arguments \(e.g. [isIP](https://github.com/chriso/validator.js#validators)\), you can define the `validate` in the following way:

```js
const entitySchema = new Schema({
  // ...
  ip  : {
    validate: {
      rule: 'isIP',
      args: [4]
    },
  }
});
```

This way, the `args` item will be passed to the as 3rd argument on the `isIP` validation function, accepting only IPs of IPv4 type.

#### Valid values

You can also define an **Array of valid values** for a properties.  
If you then try to save an entity with any other value it won't validate and won't be saved in the Datastore.

```js
const productSchema = new Schema({
    color  : {values: [ '#ffffff', '#ff6000', '#000000' ]},
    ...
});

const data = { color: '#cccccc' };
const product = new Product(data);
product.save().catch((err) => {
  // Validation Error
})
```



