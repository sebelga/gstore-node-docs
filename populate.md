# Populate

Gstore supports `populate()` which lets you reference other entities in your Schemas and easily fetch them and merge their data whenever needed. Population automatically replaces the specified paths in an entity with the data from other entities. We may populate a single entity, multiple entities, or all the entites returned from a query. We can even populate nested entities. Let's look at some examples.

## Declare references on your Schemas

The first thing to do is to add some _Key_ references to our Schemas.

```javascript
const { Schema } = gstore;

const addressSchema = new Schema({ city: { type: String }, country: { type: String } });
const blogSchema = new Schema({ title: { type: String } });
const userSchema = new Schema({
    name: { type: String },
    email: { type: String, validate: 'isEmail' },
    address: { type: Schema.Types.Key, ref: 'Address' }, // references an Address
});
const postSchema = new Schema({
    title: { type: String },
    author: { type: Schema.Types.Key, ref: 'User' } // references a User
    blog: { type: Schema.Types.Key, ref: 'Blog' } // references a Blog
});

const Address = gstore.model('Address', addressSchema);
const Blog = gstore.model('Blog', blogSchema);
const User = gstore.model('User', userSchema);
const Post = gstore.model('Post', postSchema);
```

## Save the entities with references

Let's then save some entities, providing the entity _Key_ as reference. 

```javascript

const blog = new Blog({ title: 'This is the title of the blog' });
await blog.save();

const address = new Address({ city: 'Brussels', country: 'Belgium' });
await address.save();

const user = new User({ name: 'John', email: 'john@snow.com', address: address.entityKey });
await user.save();

const post = new Post({ title: 'Title of the post', author: user.entityKey });
await post.save();
```

## Populate on Model.get()

### Populate 1 reference

In the example below, let's assume that the post we have just saved received the `123` generated ID from the Datastore.

```javascript
// With the above models delared
// When we fetch the Post by key, we can ask to populate reference entities
const post = await Post.get(123).populate('user');

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         name: 'John',
//         email: 'john@snow.com',
//         address: {...} // Datastore Key
//     }
// }
```

### Populate nested references

What if we also want to fetch the "address" from the user at the same time?

```javascript
const post = await Post.get(123)
    .populate('user')
    .populate('user.address');

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         name: 'John',
//         email: 'john@snow.com',
//         address: {
//             city: 'Brussels',
//             country: 'Belgium'
//         }
//     }
// }
```

We don't have to add the intermediate levels if we are only interested in a nested level.

```javascript
const post = await Post.get(123).populate('user.address'); // Populate second level directly

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         address: {
//             city: 'Brussels',
//             country: 'Belgium'
//         }
//     }
// }
```

### Chain populate() calls

We can chain `populate()` calls to fetch multiple references

```javascript
const post = await Post.get(123)
    .populate('user')
    .populate('blog')
    .populate('user.address');

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         name: 'John',
//         email: 'john@snow.com',
//         address: {
//             city: 'Brussels',
//             country: 'Belgium'
//         }
//     },
//     blog: {
//         title: 'This is the title of the blog'
//     }
// }
```

Or shorter by providing an **array** of references.

```javascript
const post = await Post.get(123)
    .populate(['user', 'blog'])
    .populate('user.address');

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         name: 'John',
//         email: 'john@snow.com',
//         address: {
//             city: 'Brussels',
//             country: 'Belgium'
//         }
//     },
//     blog: {
//         title: 'This is the title of the blog'
//     }
// }
```

And if you want to fetch all the references on an entity, you can simply call `populate()` withouth specifying any references. All the properties on your Schema that have their _type_ set as `Schema.Types.Key` will be fetched.

```javascript
const post = await Post.get(123).populate(); // both "user" and "blog" will be fetched

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         name: 'John',
//         email: 'john@snow.com',
//         address: {...} // Datastore Key
//     },
//     blog: {
//         title: 'This is the title of the blog'
//     }
// }
```

It also works when providing multiple IDs

```javascript
const posts = await Post.get([123, 456]).populate();

console.log(posts);

// [
// {
//     id: 123,
//     title: 'Title of the post',
//     user: {
//         id: 1,
//         name: 'John',
//         email: 'john@snow.com',
//         address: {...} // Datastore Key
//     },
//     blog: {
//         id: 1,
//         title: 'This is the title of the blog'
//     }
// },
// {
//     id: 456,
//     title: 'Title of the second post',
//     user: {
//         id: 2,
//         name: 'Mick',
//         email: 'mick@jagger.com',
//         address: {...} // Datastore Key
//     },
//     blog: {
//         id: 1,
//         title: 'This is the title of the blog'
//     }
// },
//]
```


### Specify the properties to return

`populate()` accepts a second argument to specify **one or multiple properties** to be returned from the reference entity.  

**Important:** You can't provide specific properties if you have passed an **_array_** of references as first argument.

```javascript
const post = await Post.get(123).populate('user', 'email');

console.log(post);

// {
//     title: 'Title of the post',
//     user: {
//         email: 'john@snow.com', // only "email" is returned
//     },
// }

// We can specify multiple properties
const post = await Post.get(123).populate('user', ['name', 'email']);

// But we can't specify an array of references with specific properties
const post = await Post.get(123).populate(['user', 'blog'], 'email'); // This is **not** allowed.
```

## Populate on queries

The real power of `populate()` comes **with queries** as it allows you to very easily join entities data, similar to a "Left Join" in SQL.  gstore uses [Dataloader](./cache-dataloader/dataloader.md) to fetch all the entities by keys in one call. Using Dataloader means that if multiple entities returned by the query have references pointing to **the same entity Key**, only 1 Key will be sent to `Datastore.get()` and fetched from the Datastore. It also means that the entities fetched are memoized and returned from cache if the reference Key is the same at different "depth" of the tree traversal.  
This not only means faster retrieval time but only some nice savings on your Datastore billing at the end of the month!

Let's look at some examples.

```javascript

/**
 * Let's assume that we have 3 Models with the following Schemas
 * 
 * const authorSchema = new Schema({ name: { type: String } });
 * const collectionSchema = new Schema({ name: { type: String }, author: { type: Schema.Types.Key, ref: 'Author' } });
 * const bookSchema = new Schema({
 *    title: { type: String },
 *    author: { type: Schema.Types.Key, ref: 'Author' },
 *    collection: { type: Schema.Types.Key, ref: 'Collection' }
 * });
 * 
 * So the relationship is: Book.collection ---> Collection.author ---> Author
 *                         Book.author ---> Author
 * 
 * In some cases, the "author" of a book can also be the author of the Collection.
 * Let's query the books and populate() the book.collection + book.author from the books,
 * and the nested "author" from the book.collection.
 */

Book.list()
    .populate()
    .populate('collection.author')
    .then(({ entities }) => {
        console.log(entities);
        // [
        //     {
        //         id: 1,
        //         title: 'Book 1'
        //         collection: {
        //             id: 1,
        //             title: 'Title of my collection',
        //             author: {
        //                 id: 1,
        //                 name: 'John'
        //             }
        //         },
        //         author: {
        //             id: 1,
        //             name: 'John'
        //         }
        //     },
        //     {
        //         id: 2,
        //         title: 'Book 2'
        //         collection: {
        //             id: 1,
        //             title: 'Title of my collection',
        //             author: {
        //                 id: 1,
        //                 name: 'John'
        //             }
        //         },
        //         author: {
        //             id: 2,
        //             name: 'Mick'
        //         }
        //     }
        // ]
    });
```

For the 2 "books" + its "collection" + the "author" of the books and the collections, **only 3 keys** where fetched from the Datastore: `['User', 1], ['User', 2], ['Collection', 1]`.  

Of course, if you have [activated the cache](./cache-dataloader/cache.md) with a Redis store, it would be `0` keys fetched the second time that this query is executed, until a "User", "Post" or "Collection" is added/updated or removed.

**Important:** The maxium Keys allowed for a Lookup operation is **1,000**. With `populate()`, this corresponds to the maximum number of _distinct_ entity references at **one** "depth" of the entity data tree. Thanks to the Dataloader and cache, this limit should be hard to reach in most use cases. ([View the Datastore limitations](https://cloud.google.com/datastore/docs/concepts/limits))

### Model.list(), .findAround(), .findOne(), .query()

All the different gstore queries support the `populate()` chaining calls with the same arguments that we have seen in previous examples.

```javascript
// Datastore query()
const { entities } = await Post.query()
                            .filter('createdAt', '>', new Date('2019-01-01'))
                            .run()
                            .populate('author', 'email');

// findOne()
const user = await User.findOne({ email: 'john@snow' })
                            .populate('address');

// findAround()
const users = await User.findAround('createdAt', new Date('2019-01-01'), { after: 10 })
                    .populate();
```

## Populate on an Entity instance

Finally, _Entity_ instances have also a `populate()` method that you can call to quickly fetch all the entity references. Like any other populate() that we have seen above, it takes 2 arguments: the "_references_" to fetch and the "_properties_" to return from each entity reference.

```javascript
const post = new Post({ title: 'Some title', author: SomeAuthorKey });
await post.populate('author');

// post.author has now been replaced by the content of the entity at "SomeAuthorKey"
```
