# Custom Methods
Custom methods can be attached to entities instances through their Schemas.  

`schema.methods.methodName = function(){}`

```js
var blogPostSchema = new Schema({title:{}});

// Custom method to retrieve all children Text entities
blogPostSchema.methods.texts = function(cb) {
	var query = this.model('Text')
						.query()
						.hasAncestor(this.entityKey);

	query.run(function(err, result){
		if (err) {
			return cb(err);
		}
		cb(null, result.entities);
	});
};

...

// You can then call it on an entity instance of BlogPost
BlogPost.get(123).then((data) => {
	const blogEntity = data[0];
	blogEntity.texts(function(err, texts) {
	    console.log(texts); // texts entities;
	});
});
```

Note how entities instances can access other models through `entity.model('OtherModel')`. *Denormalization* can then easily be done with a custom method:

```js
// Add custom "getImage()" method on the User Schema
userSchema.methods.getImage = function(cb) {
    // Any type of query can be done here
    // note this.get('imageIdx') could also be accessed by virtual property: this.imageIdx
    return this.model('Image').get(this.get('imageIdx'), cb);
};
...
// In your controller
var user = new User({name:'John', imageIdx:1234});

// Call custom Method 'getImage'
user.getImage(function(err, imageEntity) {
    user.profilePict = imageEntity.get('url');
    user.save().then(() { ... });
});

// Or with Promises
userSchema.methods.getImage = function() {
    return this.model('Image').get(this.imageIdx);
};
...
var user = new User({name:'John', imageIdx:1234});
user.getImage().then((data) => {
	const imageEntity = data[0];
    ...
});
```


