# Schema Options

When creating a Schema you can pass a second object with a set of options. This object is **optional** you only need to pass it to override the default values.

```js
const mySchema = new Schema({...properties}, {... options});
```

## validateBeforeSave (default true)

By default each time an entity is saved it is validated. You can disable this behaviour by setting **validateBeforeSave** to false.

## explicitOnly (default true)

To allow undeclared properties on a Schema set **explicitOnly** to false. This will bring back the magic of *Schemaless* datastores.  
The properties explicitly declared will still be validated.

## queries
Here you can set global behaviour for queries. These settings can be overridden on each query.

#### readAll (default false)
Override the Schema property parameter **read** ([here](../schema/other-parameters.md) ) to return all the properties of the entities.

