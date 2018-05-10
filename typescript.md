# Typescript support

![](/assets/typescript-gstore.png)



gstore-node support Typescript. You just need to create a custom `type` and pass it to the Schema and Model creation.

Let see it with an example

```js
// user.schema.ts

const gstore = require('gstore-node')();

type UserType = {
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

// Pass it on Model creation
const User = gstore.model<UserType>('User', schema);
```

It you want to allow **other properties** apart from those declared \(see `explicitOnly` option in the [Schema options](/schema/schema-options.md)\), this is how you should create your Model:

```js
type UserType = {
    userName: string;
    email: string;
    age?: number; // optional
    tags?: string[]; // optional
    birthday?: Date; // optional
}

// Schema with "explicitOnly" set to "false"
const schema = new Schema<UserType>({
    userName: { type: String },
    email: { type: String },
    age: { type: Number, optional: true },
    tags: { type: Array, optional: true },
    birthday: { type: Date, optional: true }
}, { explicitOnly: false });

// Allow UserTypes + any other properties on the Model
const User = gstore.model<UserType & {[propName: string]: any}>('User', schema);
```



