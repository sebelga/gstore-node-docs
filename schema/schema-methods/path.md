# Schema methods

## path\(\)

Getter / Setter for schemas paths.

```js
const mySchema = new Schema({ username: {type:'string' });

// Getter
mySchema.path('name'); // returns { type:'string' }

// Setter
mySchema.path('email', { type: 'string', validate: 'isEmail' });

// You can access the schema from a Model
const User = gstore.model('User');

// add new path to the User Schema
User.schema.path('age', { type:'number' });
```
