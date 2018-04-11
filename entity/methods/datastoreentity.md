# Entity methods

## datastoreEntity\(\)

In case you need at any moment to fetch the entity **data** from Google Datastore, this method will do just that right on the entity instance. It is useful for example in "pre" delete hooks where the entity to be deleted is passed but we don't have its data. 

```js
// user.model.js

const userSchema = new Schema({ name: { type: String }, pictIdx: { type: Number });

schema.pre('delete', function() {
    // The scope "this" is the entity to be deleted.
    // At this stage we don't have its data but we need it in order
    // to check if there is an Image to delete with the User.
    // We use datastoreEntity() for that.
    
    return this.datastoreEntity().then(entity => {
        if (!entity.pictIdx) {
            return;
        }
        // We delete the associate image entity
        return entity.model('Image').delete(entity.entity.pictIdx);
    });
});

module.exports = gstore.model('User', userSchema);

// ...

// Now each time we delete a User, we'll delete any Image associated with it.

const User = require('user.model');
User.delete(123).then(...);

```



