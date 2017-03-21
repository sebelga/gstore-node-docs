# Schema

## Types validation

You can set a _type _for a schema property adding a "type" parameter to it.

`new Schema({ name: { type: 'string' }})`

Valid types are

* string
* int \(an integer or a gcloud.datastore.int\)
* double \(a float or a gcloud.datastore.double\)
* boolean
* datetime\(\*\)
* array
* object
* geoPoint \(a gcloud.datastore.geoPoint\)
* buffer

\(\*\) Valid datetime are javascript Date\(\) or a string with the following format: 'YYYY-MM-DD' \| 'YYYY-MM-DD 00:00:00' \| 'YYYY-MM-DD 00:00:00.000' \| 'YYYY-MM-DDT00:00:00'

So back to our previous example of a User Model, we would probably have something like this.

```js
const userSchema = new Schema({
    name: { type: 'string' },
    lastname: { type: 'string' },
    password: { type: 'string' },
    email: { type: 'string' },
    votes: { type: 'int' },
    dateOfBirth: { type: 'datetime' },
    createdOn: { type: 'datetime' },
    modifiedOn: { type: 'datetime' }
});
```

Note: you are not forced to set a type, if you don't define one, then **any **type is valid.

```js
const userSchema = new Schema({
    name: {}, // any type
    email: { type: 'string' }
});
```



