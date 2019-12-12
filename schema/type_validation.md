# Type validation

You can define a type for a schema property passing a "type" parameter to it.

`new Schema({ name: { type: String }})`

Valid types are

* String
* Number \(an integer or a `gcloud.datastore.int()`\)
* Array
* Object
* Boolean
* Date\(\*\)
* Buffer
* Schema.Types.Double \(a float or a `gcloud.datastore.double()`\)
* Schema.Types.GeoPoint \(a `gcloud.datastore.geoPoint()`\)
* Schema.Types.Key \(an entity Key\)

\(\*\) Valid Date are javascript `Date`  instance \(calling`new Date()`\)  or any valid **string** date. If a string date is provided, it will automatically be parsed as Date object when saving the entity in the Datastore.

So back to our previous example of a User Model, we would probably have something like this.

```javascript
const userSchema = new Schema({
    name: { type: String },
    lastname: { type: String },
    password: { type: String },
    email: { type: String },
    company: { type: Schema.Types.Key, ref: 'Company' },
    votes: { type: Number },
    dateOfBirth: { type: Date },
    createdOn: { type: Date },
    modifiedOn: { type: Date }
});
```

Note: you are not forced to set a type, if you don't define one, then **any** type is valid.

```javascript
const userSchema = new Schema({
    name: {}, // any type
    email: { type: String }
});
```

