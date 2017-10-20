# Schema

## Joi Schema

If you need more advanced schema validation gstore-node support **Joi types and validator** for your properties. [Joi ](https://www.npmjs.com/package/joi)is a powerful schema description language with a [great API](https://github.com/hapijs/joi/blob/v13.0.1/API.md) to validate your fields. It it specially useful for complex embedded entities validation or if you need more precision on your validation.

**Important**: if you decide to use Joi, you have to use it for **all your properties**. You can not mix joi types and validator with gstore type and value validation.

You can define a Joi type and validator by setting a _joi_ setting on your schema property and set the Schema *joi* option to **true**.

```js
const { Schema } = require('gstore-node')();
const Joi = require('joi');

const userSchema = new Schema({
    name: { joi: Joi.string().required() },
    email: { joi: Joi.string().email() },
}, {
    joi: true, // don't forget to add this setting in the Schema options
});
```

Joi types can replace all the following settings of a property:

- type
- validate
- default
- write (corresponds to Joi.strip())
- values
- required

But you **still need to configure** the following settings

- excludeFromIndexes
- read

```js
const schema = new Schema({
    password: { joi: Joi.string().required(), read: false },
    longText: { joi: Joi.string(), excludeFromIndexes: true },
}, { joi: true });

```

