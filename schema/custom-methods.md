# Custom Methods

Custom methods can be added to entities instances through their Schemas.  

`schema.methods.<methodName> = function(){ ... }`

Make sure to **not** use arrow function as you would lose the scope set to the entity instance.

```js
const blogPostSchema = new Schema({ title: {} });

// Custom method to retrieve all children Text entities
blogPostSchema.methods.texts = function() {
    // the scope (this) is the entity instance
    const query = this.model('Text')
                        .query()
                        .hasAncestor(this.entityKey);

    return query.run();
};

...
// In your Controller
// You can then call it on an entity instance of BlogPost
const BlogPost = require('../models/blogpost.model');

BlogPost.get(123).then((data) => {
    const blogEntity = data[0];
    blogEntity.texts()
                .then((response) => {
                    const texts = response[0].entities;
                });
});
```

Note how entities instances can access other models through `entity.model('OtherModel')`. *Denormalization* can then easily be done with a custom method:

```js
// Add custom "profilePict()" method on the User Schema
userSchema.methods.profilePict = function() {
    return this.model('Image').get(this.imageIdx);
};

...

// In your controller
const User = require('../models/user.model');

const user = new User({ name: 'John', imageIdx: 1234 });
user.profilePict().then((data) => {
    const imageEntity = data[0];
    user.profilePict = imageEntity.url;
    user.save().then(() { ... });
});

// Or with a callback
userSchema.methods.profilePict = function(cb) {
    // Any type of query can be done here
    return this.model('Image').get(this.imageIdx, cb);
};
...

const user = new User({ name:'John', imageIdx:1234 });

// Call custom Method 'getImage'
user.profilePict(function(err, imageEntity) {
    user.profilePict = imageEntity.url;
    user.save().then(() { ... });
});
```


