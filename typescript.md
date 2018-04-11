# Typescript support

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

const schema = new Schema<UserTypes>({
    userName: { type: String },
    email: { type: String },
    age: { type: Number, optional: true },
    tags: { type: Array, optional: true },
    birthday: { type: Date, optional: true }
});

const User = gstore.model<UserTypes>('User', schema);

```