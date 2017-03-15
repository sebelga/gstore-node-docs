# Entity

Each entity is an instance of a Model and represents an entity stored in the Datastore of a certain "Entity Kind".

## Creation

To create an instance of a model use

`new Model(data, id /*optional*/, ancestors /*optional*/, namespace /*optional*/)`

- data {object} keys/values pairs of the entity data
- id {int or string} (optional)
- ancestors {Array} (optional)
- namespace {string} (optional)

#### id
**(optional)**

By default, if you don't pass an id when you create an instance, the entity id will be auto-generated. If you want to manually give the entity an id, pass it as a second parameter during the instantiation.

```js
...
// String id
var blogPost = new BlogPost(data, 'stringId');

// Integer ir
var blogPost = new BlogPost(data, 1234);
```

#### ancestors
**(optional)**

Array of an ancestor's path.

```js
// Auto generated id on an ancestor
var blogPost = new BlogPost(data, null, ['Parent', 'keyname']);

// Manual id on an ancestor
var blogPost = new BlogPost(data, 1234, ['Parent', 'keyname']);
```

#### namespace parameter
**(optional)**

By default entities keys are generated with the default namespace (defined when setting up the datastore instance). You can create models instances on
another namespace by passing it as a third parameter.

```js
// Creates an entity with auto-generated id on the namespace "dev-com.my-domain"
var blogPost = new BlogPost(data, null, null, 'dev-com.my-domain');
```
