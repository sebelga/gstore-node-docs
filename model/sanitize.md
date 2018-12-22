# Model Methods

This methods will clean and do basic formatting of an entity data. You should always execute it on data coming from an untrusted source.  MyModel.sanitize() will:

- **remove properties** that are marked as **not writable** in schemas
- convert 'null' (string) values to **null**

```js
// user.model.js

const userSchema = new Schema({
    name : { type: String },
    createdOn : { type: Date, write: false } // write is not allowed
});

// or with a Joi schema
const schema = new Schema({
    name: { joi: Joi.string() },
    createdOn: { joi: Joi.date().strip() } // strip() will remove the property when Sanitizing
}, { joi: true });

module.exports = gstore.model('User', userSchema);

```

```js
// user.controller.js
const User = require('./user.model');

let data = req.body;
console.log(data);
/*
{
    createdOn: '2016-03-01T21:30:00',
    name: 'null',
    propertyNotDefined: 'abcdef',
}
*/

// Now sanitize the body request
data = User.sanitize(req.body);
console.log(data);

/*
{
    name: null,
}
*/

```