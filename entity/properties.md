# Entity

## Properties

The entities have the following properties

* **entityData**. The data of the Entity.
* **entityKey**. The key of the Entity.

#### Virtual properties

Based on the definition of the schema, **virtual properties** are automatically added on the Entity objects to retrieve the values from the entityData directly.  
This means there are two ways you can access the data of an Entity: via entity.entityData or direct on the entity itself.

For example:

```js
const gstore = require('gstore-node')();

const blogPostSchema = new gstore.Schema({
    title: { type:'string' },
    createdOn: { type:'datetime', default:gstore.defaultValues.NOW }
});

const BlogPost = gstore.model('BlogPost', blogPostSchema);

const data = {
    title : 'My first blog post'
};
const blogPost = new BlogPost(data);

// Getter
// Logs the same value 'My first blog post'
console.log(blogPostEntity.title);
console.log(blogPostEntity.entityData.title);

// Setter
blogPostEntity.title = 'My second blog post'; // blogPostEntity.entityData.title is also changed
blogPostEntity.entityData.title = 'My third blog post'; // blogPostEntity.title is also changed
```



