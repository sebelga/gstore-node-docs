# Model Methods

## save()

After you create an entity you can persist its data to the Datastore with `entity.save()`  
This method accepts the following arguments

- transaction (optional). Will execute the save operation inside this transaction
- options { method: 'upsert | insert | update' }  (optional). Default to { method: 'upsert' }
- callback (optional, if not passed a **Promise** is returned)


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

blogPostEntity.save(function(err) {
    if (err) {// deal with err}

    // the function scope (this) is the entity instance.
    console.log(this.plain());
    console.log(this.get('title')); // 'My first blog post'
    console.log(this.entityKey.id); // to get the auto generated id
});

/*
 * From inside a transaction
 */
var user = new User({name:'john'});
var transaction = gstore.transaction();

transaction.run().then(() => {
	
	// See note below to avoid nesting Promises
   return user.save(transaction).then(() => {
    	return transaction.commit().then((data) => {
    		const apiResponse = data[0];
    		...
    	});
    });
}).catch((err) => {
	// handle error
   ...
 });


/*
 * Changing the method to save
 */

var blogPostEntity = new BlogPost(data);
blogPostEntity.save(null, { method: 'insert' }).then( ... );


```

Note on **saving inside a Transaction**  
By default, the entity data is validated before being saved in the Datastore (you can desactivate this behavious by setting [validateBeforeSave](#validateBeforeSave) to false in the Schema definition). The validation middleware is async, which means that to be able to save inside a transaction and at the same time validate before, you need to resolve the *save* method before being able to commit the transaction.  
A solution to avoid this is to **manually validate** before saving and then desactivate the "pre" middelwares by setting **preHooksEnabled** to false on the entity.  
**Important**: This solution will bypass any other middleware that you might have defined on "save" in your Schema.

```js
var user = new User({name:'john'});
var transaction = gstore.transaction();

transaction.run().then() => {
	User.get(123, null, null, transaction).then((data) => {
		const user = data[0];
		user.email = 'abc@def.com';
		const valid = user.validate();
		
		if (!valid) {
		    // exit the transaction;
		}
		
		// disable pre middleware(s)
		user.preHooksEnabled = false;
		
		// save inside transaction
		user.save(transaction);

		// ... more transaction operations
		
		transaction.commit().then(() => {
		    ...
		});
	});
});
```