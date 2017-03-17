# Entity methods

## model()

With this method you can access a Model from entity instance.

```js
// Example on a schema 'pre' save middleware
commentSchema.pre('save', function beforeSave(next){
    var User = this.model('User');
    console.log(User === UserModel); // true
});
```