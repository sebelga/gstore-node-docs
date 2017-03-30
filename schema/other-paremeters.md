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

Allows you to define a default value for a property. This value, for now, must be **static** and **is not** calculated at runtime.  For example setting the default value as `uuidV4()` from the uuid package will set **the same** uuid for all the entities \(probably not the desired behaviour\). In a future release I plan to allow a function to be executed at runtime.

Although there is already a special default value for the **current datetime** of the request: `gstore.defaultValues.NOW`

And if you have a **_modifiedOn _**property set in your schema, it will automatically be set to the current time **each time** the entity is saved.

```js
const userSchema = new Schema({
    createdOn: { type: 'datetime', default: gstore.defaultValues.NOW },
    modifiedOn: { type: 'datetime' } // will automatically be updated to the current time on each "save|update"
});
```

#### excludeFromIndexes

By default all properties are **included** in the Datastore indexes. If you don't want some properties to be indexed set their 'excludeFromIndexes' parameter  
to **true**.

```js
const articleSchema = new Schema({
    author: { type: 'string' },
    text: { type: 'string', excludeFromIndexes: true } // indexes size is limited, so very long text can't be indexed
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
    name: { type: 'string' }
    email: { type: 'string',  validate: 'isEmail', required: true }
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
    name:  {type: 'string'},
    lastname:  {excludeFromIndexes: true},
    email: {validate: 'isEmail', required: true},
    website :  {validate: 'isURL', optional: true},
    modified:  {type: 'boolean', default: false, read:false}, // won't show up in queries
    createdOn: {type:'datetime', default: gstore.defaultValues.NOW, write:false} // will be removed from data on sanitize()
    ...
});
```



