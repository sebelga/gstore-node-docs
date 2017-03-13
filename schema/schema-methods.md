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

## virtual\(\)

Virtuals are properties that are added to the entities at runtime that are not persisted in the Datastore. You can both define a **getter** and a **setter**.

**getter**

```js
const userSchema = new Schema({
    firstname: {},
    lastname : {}
});

userSchema.virtual('fullname').get(function fullName() {
    // the scope (this) is the entityData of the entity instance
    // for this reason don't use arrow functions here
    return `${this.firstname} ${this.lastname}`;
});

const User = gstore.model('User', schema);

const user = new User({ firstname:'John', lastname:'Snow' });
console.log(user.fullname); // 'John Snow';

/*
 * You can also set virtuals to true when calling plain() on your entity
 * to add them to the object returned.
 */
const response = user.plain({ virtuals: true });
console.log(response.fullname); // 'John Snow';
```

**setter**

```js
const userSchema = new Schema({
    firstname: {},
    lastname : {}
});

userSchema.virtual('fullname').set(function(name) {
    const split = name.split(' ');
    this.firstname = split[0];
    this.lastname = split[1];
});

const User = gstore.model('User', schema);

const user = new User();
user.set('fullname', 'John Snow');

console.log(user.firstname); // 'John';
console.log(user.lastname); // 'Snow';
```

**Note:** You can save entities without worrying about the virtuals as they are **removed** from the entity data automatically.

