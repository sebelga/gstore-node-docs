# Schema options

When creating a Schema you can pass a second object with a set of options. This object is **optional** you only need to pass it if you need to override the default values.

```javascript
const mySchema = new Schema({...properties}, {...options});
```

## validateBeforeSave

**default: true**

By default each time an entity is saved it is validated. You can disable this behaviour by setting **validateBeforeSave** to false.

## explicitOnly

**default: true**

To allow undeclared properties on a Schema set **explicitOnly** to false. This will bring back the magic of _Schemaless_ to your entities. The properties explicitly declared will still be validated but any other will be accepted.

## queries

An options object to set global configuration for your queries. These settings can be overridden on specific queries:

* `queries.readAll`
* `queries.format`
* `queries.showKey`
* `queries.cache`
* `queries.ttl`
* `queries.consistency`

### &gt; readAll

**default: false**

Override the Schema property parameter **read** \([here](other-paremeters.md#read)\) to return all the properties of the entities.

### &gt; format

**default: "JSON"**

By default queries will return plain JSON objects with the entity **data** + the "**id**" of the entity added automatically.  
If you prefer, you can have gstore **entity instances** returned \(with all its methods "save\(\), delete\(\)", ...\).

The response format can be defined here globally but it can be overridden later in each query.  
Valid values are:

* "JSON"
* "ENTITY"

### &gt; showKey

**default: false**

Adds a "\_\_key" property to the entity data with the complete Key from the Datastore.

### &gt; cache

**default:** the _global_ cache configuration  
"true" = read from the cache and prime the cache with the query response.

### &gt; ttl

**default:** the global `cache.ttl.queries`configuration  
Custom TTL value for the cache. For multi-store it can be an Object of TTL values.

```javascript
{
    queries: { ttl: 3600 };
}

// multi-store
{
    queries: {
        ttl: {
            memory: 600,
            redis: 3600,
        }
    }
}
```

### &gt; consistency

Specify either "strong" or "eventual". If not specified, default values are chosen by Datastore for the operation. Learn more about strong and eventual consistency [here](https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore).

## excludeLargeProperties

**default: `false`**

This will automatically exclude large properties from indexing. It help in storing large values that are not declared on the schema.

**Note:** This feature requires the `@google-cloud/datastore` **version 4.3.0 or superior.**

## Complete example:

```javascript
const userSchema = new Schema({
    username : { type: String },
    email: { type: String, validate: 'isEmail' }
}, {
    validateBeforeSave: false,
    explicitOnly: false,
    excludeLargeProperties: true,
    queries: {
        readAll: true,
        format: 'ENTITY',
        showKey: true,
    }
});
```

