# Model Methods

## sanitize()

This methods will clean and do basic formatting of an entity data. You should always execute it on data coming from an untrusted source.  MyModel.sanitize() will:

- **remove properties** that are marked as **not writable** in schemas
- convert 'null' (string) values to **null**

```js
// user.model.js

const userSchema = new Schema({
    name : { type: 'string' },
    createdOn : { type: 'datetime', write: false } // write is not allowed
});

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

// Sanitize body request
data = User.sanitize(req.body);
console.log(data);

/*
{
    name: null,
}
*/

```