#Middleware (hooks)

### Pre hooks
The middleware that you declare receives the original parameter(s) passed to the method. You can modify them in your **resolve** passing an object with an **__override** property containing the new parameter(s) for the target method (be careful though... with great power comes great responsibility!).  See example below.  
If you **reject** the Promise in a "pre" middleware, the target function is not executed.

A common use case would be to hash a user's password before saving it into the Datastore.

```js
const gstore = require('gstore-node');
const bscrypt = require('bcrypt-nodejs');

const userSchema = new gstore.Schema({
    user: { type: 'string' },
    email: { type: 'string', validate: 'isEmail' },
    password: { type: 'string', excludeFromIndexes: true }
});

// Hash password middleware
function hashPassword() {
    // scope *this* is the entity instance
    const _this = this;
    const password = this.password;

    if (!password) {
        // nothing to hash... exit
        return Promise.resolve();
    }
    
    return new Promise((resolve, reject) => {
        bcrypt.genSalt(5, function onSalt(err, salt) {
            if (err) {
                return reject(err);
            };

            bcrypt.hash(password, salt, null, function onHash(err, hash) {
                if (err) {
                    // reject will *not* save the entity
                    return reject(err);
                };

                _this.password = hash;
				
                // resolve to go to next middleware or target method
                return resolve();
            });
        });
    });
}


// add the "pre" middleware to the save method
userSchema.pre('save', hashPassword);

...

// Then when you create a new user and save it (or when updating it)
// the password will automatically be hashed

const User = require('./user.model');
const user = new User({ username: 'john', password: 'mypassword' });

user.save()
    .then((response) => {
        const entity = response[0];
        console.log(entity.password);
        // $7b$01$GE/7OqVnMyThnaGC3QfEwuQ1imjifli3MvjcP7UGFHAe2AuGzne5.
});
```

**Note**
The pre('delete') hook has its scope set on the entity to be deleted. **Except** when an *Array* of ids to delete is passed.

```js
blogSchema.pre('delete', function() {
	console.log(this.entityKey); // the datastore entity key to be deleted

	// By default this.entityData is not present because
	// the entity is *not* fetched from the Datastore.
	// You could call this.datastoreEntity() here (see the Entity section)
	// to fetch the data from the Datastore and process any other logic
	// before resolving your middlewware
	
	// Access arguments passed
	const args = Array.prototype.slice(arguments);
	console.log(args[0]); // 1234 (from call below)
	
	// Here you would override the id to delete! At your own risk...
	// The Array passed in __override are the argument(s) of the target function
	return Promise.resolve({ __override: [1235] });
});

BlogPost.delete(1234).then(() => {...});
```

You can also pass an **Array** of middlewares to execute

```js
function middleware1() {
    // Return a Promise
    return Promise.resolve();
}

function middleware2() {
    return Promise.resolve();
}

userSchema.pre('save', [middleware1, middleware2]);
```

