# Schema Options

When creating a Schema you can pass a second object with a set of options. This object is **optional** you only need to pass it to override the default values.

```js
const mySchema = new Schema({...properties}, {... options});
```

## validateBeforeSave \(default: true\)

By default each time an entity is saved it is validated. You can disable this behaviour by setting **validateBeforeSave** to false.

## explicitOnly \(default: true\)

To allow undeclared properties on a Schema set **explicitOnly** to false. This will bring back the magic of _Schemaless_ datastores.  
The properties explicitly declared will still be validated.

## queries

An object to set global settings for your queries. These settings can be overridden on each query:

#### readAll (default: false\)

Override the Schema property parameter **read** \([here](../schema/other-paremeters.md#read)\) to return all the properties of the entities.

#### format (default: gstore.Queries.formats.JSON)  

By default queries will return plain JSON objects with the entity **data** + the "**id**" of the entity added automatically.  
If you prefer, you can also have entities gstore **instances** returned (with all the extra methods like "save(), model()", ...).  
The response format can be set here globally but this setting can be overriden on any query (see below).  
Valid values are: 

