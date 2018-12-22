# Entity methods

This methods validates an entity data.

**@Returns** an object with an **error** and a **value** property

If the error is null, it is valid. The value returned is the entityData sanitized (unknown properties removed).

```js
const schema = new Schema({ firstname: { type: String }, lastname: { type: String } });
const User = gstore.model('User', schema);

const user = new User({ firstname: 'John', lastname: 'Snow' });
const { error } = user.validate();

// error === null --> valid

```

The validate() method also returns **a Promise** if you need to chain it

```js
const user = new User({ firstname: 'John', lastname: 'Snow' });

user.validate()
    .then((value) => {
        // value is the entityData sanitized
    }, (err) => {
        // the validation Error
    });
 
``` 
