# Entity methods

## validate()

This methods validates an entity data. Return true if valid, false otherwise.

```js
const schema = new Schema({ firstname: { type: 'string' }, lastname: { type: 'string' } });
const User = gstore.model('User', schema);

const user = new User({ firstname:'John', lastnaaame:'Snow' });
const valid = user.validate();

console.log(valid); // false
```
