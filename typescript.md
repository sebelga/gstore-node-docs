# Typescript support

![](/assets/typescript-gstore.png)

gstore-node supports Typescript. Make sure you have installed the Types definition file for @google-cloud/datastore first:

```sh
npm install --save @types/google-cloud__datastore 
```

Once you have installed the types for google Datastore, you just need to create a custom `Type` for your schema and pass it to the Schema and Model instance.

Let see it with an example

```js
// user.schema.ts

import GstoreNode from 'gstore-node';

const gstore = GstoreNode();

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

It you want to allow **other properties** apart from those declared \(see `explicitOnly` option in the [Schema options](/schema/schema-options.md)\), this is how you would create your Model:

```js
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

const User = gstore.model<UserType>('User', schema);
```



