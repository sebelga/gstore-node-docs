# Additional properties settings

### optional

By default if a value is not provided it will be set to null or to its _default \_value \(if any\). If you don't want this behavior you can define it as \_optional_ and if no value is passed, this property will not be saved in the Datastore.

```javascript
const schema = new Schema({
    ...
    website: { optional: true }
});
```

### default

Allows you to define a default value for a property.  
You can either pass a **static** value or a **function** to be executed at runtime.

In case you want to set the current time of the request to _date_ property you can use the `gstore.defaultValues.NOW` for default value \(instead of writing a function\).

Also, if you have a \_modifiedOn \_property set in your schema, it will automatically be set to the **current time of the request** when the entity is saved.

```javascript
const userSchema = new Schema({
    createdOn: { type: Date, default: gstore.defaultValues.NOW }, // will be set to the current time of the request
    modifiedOn: { type: Date }, // will automatically be updated to the current time on each "save|update"
    randomId: { default: () => uuidV4(), write: false } // function executed at runtime
});
```

### excludeFromIndexes

By default all properties are **included** in the Datastore indexes. If you don't want some property to be indexed set its `excludeFromIndexes` option to **true**.

```javascript
const articleSchema = new Schema({
    author: { type: String },
    text: { type: String, excludeFromIndexes: true } // indexes size is limited, so very long text can't be indexed
});
```

For **embedded entities** you can pass one or more properties that you don't want to index by passing the property name or an Array of names.

```javascript
// In the example below, 'biography' is a property of the embedded entity 'author'
// and 'text' & 'description' are properties of an implicit 'book' embedded entity in the array
// Important: for embedded entities inside **Arrays** you have to specify the type to 'Array' for the excludeFromIndexes to work.

const mySchema = new Schema({
    author: { type: Object, excludeFromIndexes: 'biography' },
    publication: { type: Object, excludeFromIndexes: '*' }, // Wildcard for all object properties
    other: { type: Object, excludeFromIndexes: 'childObject.*' }, // Wildcard on a "child" object
    listBooks: { type: Array, excludeFromIndexes: ['text', 'description'] },
    anotherList: { type: Array, excludeFromIndexes: '*' },
});

// The above schema would allow to save the following data:

const data = {
  author: {
    name: 'John',
    biography: '<Some very long text (more than 1500 bytes)>'
  },
  publication: {
    description: '<Some very long text'>,
    history: '<Some very long text'>,
  },
  other: {
    childObject: {
      text: '<Some very long text>',
      text2: '<Some very long text>',
    }
  },
  listBooks: [{
    text: '<Some very long text>',
    description: '<Some very long text>',
  }],
  anotherList: [{
    text: '<Some very long text>',
    description: '<Some very long text>',
  }],
};
```

### excludeFromRead

For **embedded entities** you can provide an array of properties that you don't want to be returned in the queries result or when calling `entity.plain()`

```javascript
const articleSchema = new Schema({
    author: { type: String },
    book: { type: Object, excludeFromRead: ['secret'] }
});

const ArticleModel = gstore.model('Article', articleSchema);
const article = new ArticleModel({ author: 'John', book: { title: 'Book title', secret: 'some secret value' } });

console.log(article.plain()); // { author: 'John', book: { title: 'Book title' } };

// Or from the result of a Query
ArticleModel.list()
    .then(({ entities }) => {
        entities.forEach((article) => {
            console.log(article.book.secret); // undefined
        })
    })
```

### read

If you don't want certain properties to show up in the response data of queries or when calling entity.plain\(\) \(see Entity section\), set this parameter to **false**. This is useful when you have entity properties only useful to your business logic and that you don't want to exposed publicly.

This parameter can be overridden on a query basis by passing a _readAll_ option set to **true** in:

* `entity.plain({ readAll:true });` \(see Entity section\)
* **globally** in list\(\) and a Schema _queries_ settings
* **inline** option of list\(\), query\(\) and findAround\(\) queries

### write

If you want to protect certain properties to be written by a untrusted source, you can set their _write_ parameter to **false**. You can then call **sanitize\(\)** \(see Model section\) on a Model passing the user data and those properties will be removed from the data to be saved in the Datastore.

```javascript
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

### required

If you want to define a mandatory property, set its **required** parameter to true. If the value passed for property is **undefined**, **null** or an **empty string** it will not validate and will not be saved in the Datastore.

```javascript
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

### ref

If you have specified a Schema.Types.Key as a type, you can optionally provide the Entity _Type_ that you want the Key to be of. If you don't specify a ref, any valid Datastore Key is allowed.

```text
const userSchema = new Schema({
    name: { type: String }
    address: { type: Schema.Types.Key,  ref: 'Address' } // Must point to an Address entity Kind
});

// Could be used like this

const User = require('./user.model');
const Address = require('./address.model');

const user = new User({ name: 'John', address: Adress.key(123) });

// Or first by saving a new address an then create a User with its Key

const address = new Addresss({ country: 'Belgium' });
const { entityKey } = await address.save();
const user = new User({ name: 'John', address: entityKey });
await user.save();
...
```

## Complete parameters example

```javascript
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

