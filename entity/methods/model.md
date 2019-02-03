# model\(\)

With this method you can access a Model from an entity instance.

```javascript
const BlogPost = require('blog-post.model');
const blogEntity = new BlogPost({ title: 'Blog title' });

blogEntity.save().then(() => {
    const Image = blogEntity.model('Image'); // access the 'Image' Model
    const imageEntity = new Image({ uri: 'http://domain.com/image.jpg' });
    ...
});

// Example on middleware (schema 'pre' save)
commentSchema.pre('save', function beforeSave(){
    // Reminder: in a "save" middelware, the scope (this) is the entity being saved
    const User = this.model('User');
    return User.get(this.entityData.userIdx)
               .then(() => {
                    ...   
                });
});
```

