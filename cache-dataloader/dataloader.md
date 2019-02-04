# Dataloader

[Dataloader](https://github.com/facebook/dataloader) is a utility from Facebook to "reduce requests to the backends via batching and caching".

It combines all individuals loads within a single frame of execution \(a single tick of the event loop\) and then calls its batch function with all requested keys, making sure we don't hit the backend asking for the same key twice.

Although Dataloader has a memoization cache for all loads which occur in a single request, it is not meant to be used as a cache layer. As it excplicitly says, Dataloader "is a data loading mechanism, and its cache only serves the purpose of not repeatedly loading the same data in the context of a single request to your Application".

## How it works

First make sure that you have dataloader installed.

```bash
npm install --save dataloader
```

### gstore.createDataLoader\(\)

createDataLoader\(\) is a **helper** to create a Dataloader instance with a batch function that works with the Google Datastore.  
You will need to create a new Dataloader instance **for each request**, and pass it to gstore methods as an option property.

Let's see it with some examples.

```javascript
// user.routes.js

const gstore = require('gstore-node');
const User = require('../users/users.model');

const { createDataLoader } = gstore;

const getUser = (req, res) => {
    const dataloader = createDataLoader();
    User.get(req.params.id, null, null, null, { dataloader })
        .then((entity) => { ... });
};
```

If you only had this piece of code then the Dataloader would not be of much help. Let's see with a more complex example

```javascript
// images.js

const Image = require('./image.model');

const getImage = (id, dataloader) => {
    return Image.get(id, null, null, null, { dataloader });
};

module.exports = { getImage };

// -------------

// users.js
const User = require('./user.model');

const getUser = (id, dataloader) => {
    return User.get(id, null, null, null, { dataloader });    
};

module.exports = { getUser };

// -------------

/**
 * The above 2 files don't know about each other (and should not).
 * In our book route handler we need to fetch the book and then its author + cover image
 */
 const gstore = require('gstore-node')();

 const images = require('../images/images');
 const users = require('../images/images');
 const Book = require('./book.model');

 const getBook = (req, res) => {
     const dataloader = gstore.createDataLoader();

     Book.get(req.params.id, null, null, null, { dataloader })
         .then((book) => (
             Promise.all([
                 // Those 2 loads will be combined and only 1 request will hit the Datastore
                 images.getImage(book.cover, dataloader),
                 users.getUser(book.author, dataloader),
             ]).then((entities) => {
                 book.cover = entities[0];
                 book.author = entities[1];

                 res.json(book);
             });
         ));
 };
```

### Dataloader memoization cache

To understand memoization, let's see it with another example.

```javascript
const Post = require('./posts.model');

// dataloader is passed from the request to our getPost() method
const getPost = async(id, dataloader) => {
    const post = await Post.get(id, null, null, null, { dataloader });
    const user = await post.model('User').get(post.author, null, null, null, { dataloader });

    console.log(user.id); // 123

    post.author = user;
    return post;
};

// ...

// Elsewhere in your application

const User = require('.users.model');

// the same instance of dataloader is passed to updateUser()
const updateUser = (id, data, dataloader) => {
    console.log(id); // 123 (same user as above);

    /**
     * Dataloader already has the user with id "123" in its memoization cache
     * so during the entity update gstore won't hit the Datastore to fetch the entity
     * before updating its data.
     * gstore automaticaly clears the memoization cache after the entity has been updated.
     */
    return User.update(id, data, null, null, null, { dataloader })
};
```

