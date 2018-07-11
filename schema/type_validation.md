# Schema

## Types validation

You can define a type for a schema property passing a "type" parameter to it.

`new Schema({ name: { type: String }})`

Valid types are

* String
* Number \(an integer or a gcloud.datastore.int\)
* Array
* Object
* Boolean
* Date\(\*\)
* Buffer
* Schema.Types.Double \(a float or a gcloud.datastore.double\)
* Schema.Types.GeoPoint \(a gcloud.datastore.geoPoint\)

\(\*\) Valid datetime are javascript Date\(\) or a string with the following format: 'YYYY-MM-DD' \| 'YYYY-MM-DD 00:00:00' \| 'YYYY-MM-DD 00:00:00.000' \| 'YYYY-MM-DDT00:00:00'

So back to our previous example of a User Model, we would probably have something like this.

```js
const userSchema = new Schema({
    name: { type: String },
    lastname: { type: String },
    password: { type: String },
    email: { type: String },
    votes: { type: Number },
    dateOfBirth: { type: Date },
    createdOn: { type: Date },
    modifiedOn: { type: Date }
});
```

Note: you are not forced to set a type, if you don't define one, then **any **type is valid.

```js
const userSchema = new Schema({
    name: {}, // any type
    email: { type: String }
});
```



