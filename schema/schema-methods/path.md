# Schema methods

## path\(\)

Getter / Setter for schemas paths.

```js
const mySchema = new Schema({ username: {type: String });

// Getter
mySchema.path('name'); // returns { type: String }

// Setter
mySchema.path('email', { type: String, validate: 'isEmail' });

// You can access the schema from a Model
const User = gstore.model('User');

// add new path to the User Schema
User.schema.path('age', { type: Number });
```
