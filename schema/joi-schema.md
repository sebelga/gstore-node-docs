# Joi Schema

If you need more advanced schema validation gstore-node support **Joi types and validator** for your properties. [Joi ](https://www.npmjs.com/package/joi)is a powerful schema description language with a [great API](https://github.com/hapijs/joi/blob/v13.0.1/API.md) to validate your fields. It it specially useful for complex embedded entities validation or if you need more precision on your validation.

**Important**: if you decide to use Joi, you have to use it for **all your properties**. You **cannot** mix joi and gstore types and validation.

## Install

First make sure you have Joi installed

```shell
  npm install joi --save
  # or
  yarn add joi
```

You can define a Joi type and validator by setting a _joi_ setting on your schema property and set the Schema _joi_ option to **true**.

```javascript
const { Schema } = gstore;
const Joi = require('joi');

const userSchema = new Schema({
    name: { joi: Joi.string().required() },
    email: { joi: Joi.string().email() },
}, {
    joi: true, // don't forget to add this setting in the Schema options
});
```

Joi types can replace all the following settings of a property:

* type
* validate
* default
* values
* required

But you **still need to configure** the following settings

* excludeFromIndexes
* read
* write

```javascript
const schema = new Schema({
    password: { joi: Joi.string().required(), read: false },
    createdAt: { joi: Joi.date().default(Date.now), write: false },
    longText: { joi: Joi.string(), excludeFromIndexes: true },
}, { joi: true });
```

## Advanced

If you need even more control over the schema validation you can define an _**extra**_ ****setting for joi. This will be applied on the complete Joi Schema object type.

```javascript
const schema = new Schema({
    username: { joi: Joi.string().alphanum().min(3).max(30).required() },
    password: { joi: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/) },
    access_token: { joi: [Joi.string(), Joi.number()] },
    birthyear: { joi: Joi.number().integer().min(1900).max(2013) },
    email: { joi: Joi.string().email() },
}, {
    joi: {
        extra: {
            with: ['username', 'birthyear'], // if username present, birthyear must be too
            without: ['password', 'access_token'], // if password present, access_token cannot
        },
    },
});
```

## Options

The validate\(\) method in Joi [accepts an options object](https://github.com/hapijs/joi/blob/v13.0.1/API.md#validatevalue-schema-options-callback). You can pass it in the Schema options.

```text
const schema = new Schema({
    username: { joi: Joi.string().alphanum().min(3).max(30).required() },
    email: { joi: Joi.string().email() },
}, {
    joi: {
        options: {
            abortEarly: false,
            allowUnknown: true,
        },
    },
});
```

