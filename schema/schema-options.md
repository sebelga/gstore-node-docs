# Schema Options

When creating a Schema you can pass a second object with a set of options.

```js
const mySchema = new Schema({...properties}, {... options});
```

## validateBeforeSave (default true)

By default each time an entity is saved it is validated. You can disable this behaviour by setting **validateBeforeSave** to false.

## explicitOnly (default true)
To allow undeclared properties on a Schema set `explicitOnly : false`. This will bring back the magic of *Schemaless* datastores.  
The properties explicitly declared will still be validated.