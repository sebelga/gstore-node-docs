#Entity

## Properties

- **entityData**. The properties data of the entity.
- **entityKey**. The entity key saved in the Datastore.

Based on the definition of the schema, virtual properties are automatically added on the Entity objects to retrieve the values from the entityData directly.
This means there are two ways you can access the data of an Entity: via entity.entityData or direct on entity itself. For example:

```js
var gstore = require('gstore-node');

var blogPostSchema = new gstore.Schema({
    title :     {type:'string'},
    createdOn : {type:'datetime', default:new Date()}
});

var BlogPost = gstore.model('BlogPost', blogPostSchema);

var data = {
    title : 'My first blog post'
};
var blogPostEntity = new BlogPost(data);

// Logs the same value 'My first blog post'
console.log(blogPostEntity.title);
console.log(blogPostEntity.entityData.title);

blogPostEntity.title = 'My second blog post'; // blogPostEntity.entityData.title is also changed
blogPostEntity.entityData.title = 'My third blog post'; // blogPostEntity.title is also changed
```