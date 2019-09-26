# Typescript

![](.gitbook/assets/typescript-gstore.png)



`gstore`  works great with Typescript projects. You simply need to create a  `Type` for your schema and pass it when instantiating your Schema.

Let see it with an example

```javascript
// user.schema.ts

import GstoreNode from 'gstore-node';

const gstore = GstoreNode();

interface UserType {
    userName: string;
    email: string;
    age?: number; // optional
    tags?: string[]; // optional
    birthday?: Date; // optional
}

// Pass it on Schema creation
const schema = new Schema<UserType>({
    userName: { type: String },
    email: { type: String },
    age: { type: Number, optional: true },
    tags: { type: Array, optional: true },
    birthday: { type: Date, optional: true }
});

const User = gstore.model('User', schema);
```

The \`UserType\` interface is then read in many places

```javascript
// Entity
const user = new User({ name: 'John' }); // TS error as "email" is not provided

// Queries
const query = User.query();
query.filter('age', '<', 'wrong string'); // TS error as "age" should be a number
query.filter('wrongProperty',  'some value'); // TS error as "wrongProperty" does not exist in User

// We can also specify the format of the query response ("JSON" is the default and thus optional)
const query = User.query<'ENTITY'>(); // Set the response format to "ENTITY"

query.run({ format: 'ENTITY' }).then(result => {
    const { entities } = result;
    const [user] = entities;
    
    // "user" is an Entity instance with its methods and properties
    user.save();
    const name = user.name; // string
});

// Shortcut queries (`list()` and `findAround()`) response
// are automatically typed from the `format` option provided
User.list({ format: 'ENTITY' }).populate().then((result) => {
    const { entities } = result;
    const [user] = entities;
    const age = user.age; // number
    user.save(); // method from the entity instance
});
```

### Non explicit schemas

It you want to allow **other properties** apart from those declared \(see `explicitOnly` option in the [Schema options](schema/schema-options.md)\), this is how you would create your Model:

```javascript
type UserType = {
    userName: string;
    email: string;
    age?: number; // optional
    tags?: string[]; // optional
    birthday?: Date; // optional
} & {[propName: string]: any}; // Allow any other properties

// Schema with "explicitOnly" set to "false"
const schema = new Schema<UserType>({
    userName: { type: String },
    email: { type: String },
    age: { type: Number, optional: true },
    tags: { type: Array, optional: true },
    birthday: { type: Date, optional: true }
}, { explicitOnly: false }); // explicitOnly set to "false"

const User = gstore.model('User', schema);
```

