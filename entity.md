# Entity

Each entity is an instance of a Model and represents an entity stored in the Datastore of a certain _Kind_.

## Creation

To create an entity you call the Model constructor

```js
new Model(
    /* {object} -- The entity data to be saved */
    <data>,
    /* {int|string}. -- optional. The id of the entity.
                        If not passed the Datastore will automatically 
                        generate a random one
     */
    <id>,
    /* {Array} -- optional. Ancestors of the entity. ex: ['ParentEntity', 1234 ] */
    <ancestors>,
    /* {string} -- optional. A specific namespace */
    <namespace>
)

example:
const blogPost = new BlogPost({title: 'title of the post', author: 'John Snow' });
```

#### id
**(optional)**

By default, if you don't pass an id when you create an instance, the entity id will be auto-generated. If you want to manually give the entity an id, pass it as a second parameter during the instantiation. 







`js
...
// String id
const blogPost = new BlogPost(data, 'stringId');

// Integer ir
const blogPost = new BlogPost(data, 1234);
```



#### ancestors
**(optional)**

You can define the ancestors of the entity.

```js
// Auto generated id with an ancestor
const blogPost = new BlogPost(data, null, ['Parent', 'keyname']);

// Manual id on an ancestor
const blogPost = new BlogPost(data, 1234, ['Parent', 'keyname']);
```

#### namespace
**(optional)**

By default entities keys are generated with the default namespace (defined when setting up the '@google-cloud/datastore'). You can create models instances on another namespace by passing it as a third parameter.

```js
// Creates an entity with auto-generated id on the namespace "dev-com.my-domain"
const blogPost = new BlogPost(data, null, null, 'dev.com.my-domain');
```
