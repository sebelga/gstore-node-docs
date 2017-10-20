# Schema

## Joi Schema

If you need more advanced schema validation gstore-node support **Joi types and validator** for your properties. [Joi ](https://www.npmjs.com/package/joi)is a powerful schema description language with a [great API](https://github.com/hapijs/joi/blob/v13.0.1/API.md) to validate your fields. In can be specially useful for complex embedded entities.

**Important**: if you decide to use Joi, you have to use it for **all your properties**. You can not mix joi type and validator with gstore type and value validation.

In define a Joi type and validator by setting a _joi_ setting on your schema property.

```js
const { Schema } = require('gstore-node')();
const Joi = requirE('joi');

const userSchema = new Schema({
    name: { joi: Joi.string().required() },
    email: { joi: Joi.string().email() },
}, {
    joi: true, // You also need to add this setting in the Schema options
});
```

