# Schema

## Properties parameters

#### optional

By default if a schema property value is not defined it will be set to null or to its default value \(if any\). If you don't want this behaviour you can define it as _optional_ and if no value is passed, this property will not be saved in the Datastore.

```js
const schema = new Schema({
    ...
    website: { optional: true }
});
```

#### default

Allows you to define a default value for a property. 
You can either pass a **static** value or a **function** to be executed at runtime.

In case you want to set the current time of the request to _date_ property you can use the `gstore.defaultValues.NOW` for default value (instead of writing a function).

Also, if you have a **_modifiedOn _**property set in your schema, it will automatically be set to the current time **each time** the entity is saved.

```js
const userSchema = new Schema({
    createdOn: { type: Date, default: gstore.defaultValues.NOW }, // will be set to the current time of the request
    modifiedOn: { type: Date }, // will automatically be updated to the current time on each "save|update"
    randomId: { default: () => uuidV4(), write: false } // function executed at runtime
});
```

#### excludeFromIndexes

By default all properties are **included** in the Datastore indexes. If you don't want some properties to be indexed set their 'excludeFromIndexes' parameter  
to **true**.

```js
const articleSchema = new Schema({
    author: { type: String },
    text: { type: String, excludeFromIndexes: true } // indexes size is limited, so very long text can't be indexed
});
```

For **embedded entities** you can pass one or more properties that you don't want to index by passing the property name or an Array of names.

```js

// In the example below, 'biography' is a property of the embedded entity 'author'
// and 'text' & 'description' are properties of an implicit 'book' embedded entity in the array
// Important: for embedded entities inside **Arrays** you have to specify the type to 'array' for the excludeFromIndexes to work.

const mySchema = new Schema({
    author: { type: Object, excludeFromIndexes: 'biography' },
    listBooks: { type: Array, excludeFromIndexes: ['text', 'description'] },
});

```

#### read

If you don't want certain properties to show up in the response data of queries or when calling entity.plain\(\) \(see Entity section\), set this parameter to **false**. This is useful when you have entity properties only useful to your business logic and that you don't want to exposed publicly.

This parameter can be overridden on a query basis by passing a _readAll_ option set to **true** in:

* `entity.plain({ readAll:true });` \(see Entity section\)
* **globally** in list\(\) and a Schema _queries_ settings
* **inline** option of list\(\), query\(\) and findAround\(\) queries

#### write

If you want to protect certain properties to be written by a untrusted source, you can set their _write_ parameter to **false**. You can then call **sanitize\(\)** \(see Model section\) on a Model passing the user data and those properties will be removed from the data to be saved in the Datastore.

```js
// Schema:
const blogPostSchema = new Schema({
    ...
    protectedProp: { write: false }
});

// In a Controller:
const BlogPostModel = require('../models/blogpost.model');

function createBlogPost(req, res) {
    let data = req.body; // {title: 'Title of the post', protectedProp: 1234};

    // Sanitize incoming user data
    data = BlogPostModel.sanitize(data);
    console.log(data); // {name: 'John', lastname: 'Snow'};

    ...
}
```

#### required

If you want to define a mandatory property, set its **required** parameter to true. If the value passed for property is **undefined**, **null** or an **empty string** it will not validate and will not be saved in the Datastore.

```js
// Schema:
const userSchema = new Schema({
    name: { type: String }
    email: { type: String,  validate: 'isEmail', required: true }
});

// In a Controller request:
var data = req.body; // {name: 'John'}; // ---> email is missing

var user = new User(data);
user.save()
    .catch((err) => {
        // --> ValidatorError
    });
```

### Complete parameters example

```js
var entitySchema = new Schema({
    name:  {type: String},
    lastname:  {excludeFromIndexes: true},
    email: {validate: 'isEmail', required: true},
    website :  {validate: 'isURL', optional: true},
    modified:  {type: Boolean, default: false, read:false}, // won't show up in queries
    createdOn: {type: Date, default: gstore.defaultValues.NOW, write:false} // will be removed from data on sanitize()
    ...
});
```



