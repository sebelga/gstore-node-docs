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

#### Custom validation function

If you have an embedded entity and you want to validate its properties you need to create a **custom validation function** for that. You then set your function as a "rule" and you can also pass additional arguments.  
Your custom validation function will receive as first argument the **value**, as second argument the **validator.js** _instance_ and then the arguments you define.

```js
const entitySchema = new Schema({
  // ...
  propWithEmbedded: {
    validate: {
      rule: customValidationFunction, // you custom validation function
      args: [2, 8] // will be passed as "min" and "max" below
    },
  }
});

function customValidationFunction(obj, validator, min, max) {
    if ('embeddedEntity' in obj) {
        const { value } = obj.embeddedEntity;
        return validator.isNumeric(value.toString()) && (value >= min) && (value <= max);
    }

    return false;
}

// You can now validate an entity with this data
const data = {
  propWithEmbedded: {
    embeddedEntity: {
      value: 6,
    },
  },
};
const model = new MyModel(data);

model.save()
      .then(() => { ... })
      .catch((err) => {
          // If there is any validation error while saving
          // it will be returned here
      });
```

#### Valid values

You can also define an **Array of valid values** for a properties.  
If you then try to save an entity with any other value it won't validate and won't be saved in the Datastore.

```js
const productSchema = new Schema({
    color  : { values: [ '#ffffff', '#ff6000', '#000000' ] },
    ...
});

const data = { color: '#cccccc' };
const product = new Product(data);
product.save().catch((err) => {
  // Validation Error
})
```



