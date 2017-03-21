#Middleware (hooks)

Middleware or 'Hooks' are functions that are executed right before or right after a specific action on an entity.  
Hooks are available for the following methods

- Entity.save() (also executed on Model.**update()**)
- Model.delete()
- Model.findOne()
- On your custom methods

Your hooks must return a Promise **and** you must use the "Promised" version of the methods (save, delete, findOne...). 



### Post hooks
Post are defined the same way as pre hooks. The main difference is that if you reject the Promise because of an error, the original method still resolves but the response is now an object with 2 properties. The **result** and **errorsPostHook** containing possible post hooks error(s).

```js
var schema = new Schema({username:{...}});
schema.post('save', function(){
    var email = this.get('email');
    // do anything needed, maybe send an email of confirmation?
    
    // If there is any error you'll reject your middleware
    return Promise.reject({ code:500, message: 'Houston something went really wrong.' });
});

// ....

var user = new User({ name: 'John' });

user.save().then((data) => {
	// You should only do this check if you have post hooks that can fail
	const entity = data.errorsPostHook ? data[0].result : data[0];
	
	if (data.errorsPostHook) {
		console.log(data.errorsPostHook[0].message); // 'Houston something went really wrong.'
	}
});

```

**Note**
The post('delete') hook does not have its scope mapped to the entity as it is not fetched from the datastore. Althought the *data* argument of the hook contain the key(s) of the entitie(s) deleted.

```js
schema.post('delete', function(data){
	// data[1] can be one Key or an array of entity Keys that have been deleted.
	return Promise.resolve();
});
```

You can also pass an **Array** of middleware to execute

```js
function middleware1() {
	return Promise.resolve();
}

function middleware2() {
	return Promise.resolve();
}

userSchema.post('save', [middleware1, middleware2]);

var user = new User({username:'john', password:'mypassword'});
user.save().then((result) => { ... });
```

### Transactions and Hooks

When you save or delete an entity from inside a transaction, gstore adds an **execPostHooks()** method to the transaction instance.  
If the transaction succeeds and you have any post('save') or post('delete') hooks on any of the entities modified during the transaction you need to call this method to execute them.

```js
var transaction = gstore.transaction();

transaction.run().then(() => {
    var user = new User({name:'john'});
    user.preHooksEnabled = false; // disable "pre" hooks (see entity section)
    user.save(transaction);

    BlogPost.delete(123, null, null, transaction);

    transaction.commit().then((data) => {
        transaction.execPostHooks().then(() => {
            const apiResponse = data[0];
        	  // all done!
        });
    });
});

```