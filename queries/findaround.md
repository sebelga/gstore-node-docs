# findAround\(\)

Find entities before or after an entity based on a property and a value.

```javascript
MyModel.findAround(
    /* {string}. -- The property to look around */
    <property>,
    /* {string} -- The property value */
    <value>,
    /* {object} -- { before|after: Number [, ... ] } */
    <options>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

The **options** argument is an object that _must_ contain _either_ a "before" or an "after" property setting the limit of entities to retrieve.

Examples:

```javascript
// Find the next 20 post after march 1st
BlogPost.findAround('publishedOn', '2016-03-01', { after: 20 })
        .then((entities) => {
            ...
        });

// Find 10 users with the lastname coming before 'Jagger'
User.findAround('lastname', 'Jagger', { before: 10 })
    .then( ... );

// with a callback
BlogPost.findAround('publishedOn', '2016-03-01', {after:20}, function onEntities(err, entities){
   ...
});
```

**Additional options**

* **readAll** {boolean} true \| false If set to true and with format set to "JSON" \(default\) it will output all the entity properties regardless of the _read_ parameter defined in the Schema.
* **format** {string} "JSON" \(default\) \| "ENTITY"
* **showKey**\(default: false\) Adds a "\_\_key" property to the entity data with the complete Key from the Datastore.
* **cache** \(default: the "global" cache configuration\) "true" = read from the cache and prime the cache with the query response.
* **ttl** \(default: the global `cache.ttl.queries` value\) Custom TTL value for the cache. For multi-store it can be an _Object_ of TTL values.
* **consistency** Specify either "strong" or "eventual". If not specified, default values are chosen by Datastore for the operation. Learn more about strong and eventual consistency [here](https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore).

```javascript
const options = { after: 20, readAll: true, format: "ENTITY" };
BlogPost.findAround('publishedOn', '2016-03-01', options)
        .then((entities) => { ... });

// disable caching
const options = { after: 20, cache: false };
BlogPost.findAround('publishedOn', '2016-03-01', options).then(...);

// set TTL for this query
// ttl can also be an object for multi-store ---> { memory: 600, redis: 3600 }
const options = { after: 20, ttl: 600 };
BlogPost.findAround('publishedOn', '2016-03-01', options).then(...);
```

