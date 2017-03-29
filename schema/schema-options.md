# Schema

## Options

When creating a Schema you can pass a second object with a set of options. This object is **optional** you only need to pass it if you need to override the default values.

```js
const mySchema = new Schema({...properties}, {... options});
```

#### validateBeforeSave

**default: true**

By default each time an entity is saved it is validated. You can disable this behaviour by setting **validateBeforeSave** to false.

#### explicitOnly

**default: true**

To allow undeclared properties on a Schema set **explicitOnly** to false. This will bring back the magic of _Schemaless_ datastores.  
The properties explicitly declared will still be validated.

#### queries

An object to set global settings for your queries. These settings can be overridden on each query:

##### readAll

**default: false**

Override the Schema property parameter **read** \([here](../schema/other-paremeters.md#read)\) to return all the properties of the entities.

##### format

**default: "JSON"**

By default queries will return plain JSON objects with the entity **data** + the "**id**" of the entity added automatically.  
If you prefer, you can have **gstore instances** of the entities returned \(with all the extra methods like "save\(\), model\(\)", ...\).

The response format can be set here globally but it can be overriden later in each query.  
Valid values are:

* "JSON"
* "ENTITY"

##### showKey

**default: false**

Adds a "__key" property to the entity data with the complete Key from the Datastore.

----

Example:

```js
const userSchema = new Schema({
    username : { type: 'string' },
    email: { type: 'string', validate: 'isEmail' }
}, {
    validateBeforeSave: false,
    explicitOnly: false,
    queries: {
        readAll: true,
        format: 'ENTITY'
    }
});
```



