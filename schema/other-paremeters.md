# Other parameters

## optional

By default if a schema property value is not defined it will be set to null or to its default value (if any). If you don't want this behaviour you can define it as *optional* and if no value is passed, this property will not be saved in the Datastore.

```js
const schema = new Schema({
    ...
    website: { optional: true }
});
```

## default

Allows you to define a default value for a property. This value, for now, must be **static** and **is not** calculated at runtime.  For example setting the default value as `uuidV4()` from the uuid package will set **the same** uuid for all the entities (probably not the desired behaviour). In a future release I plan to allow a function to be executed at runtime.

Although there is already a special default value for the **current datetime** of the request: `gstore.defaultValues.NOW`

And if you have a modifiedOn property set in your schema, it will automatically be set to the current time each time the entity is saved.

```js
const userSchema = new Schema({
    createdOn: { type: 'datetime', default: gstore.defaultValues.NOW },
    modifiedOn: { type: 'datetime' } // will automatically be set to the current time
});

```
