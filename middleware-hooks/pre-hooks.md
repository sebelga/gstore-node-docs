# Middleware \(hooks\)

### Pre hooks

Add methods to execute before "save", "delete", "findOne" or your customMethod. The middleware that you declare receives the original argument\(s\) passed to the method. You can modify them in your **resolve** passing an object with an **\_\_override** property containing the new parameter\(s\) for the target method \(be careful though... with great power comes great responsibility!\).  See example below.  
If you **reject** the Promise in a "pre" middleware, the target function is not executed.

#### Example

Hook to hash a user's password before saving it into the Datastore.

```js
const gstore = require('gstore-node')();
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
The pre\('delete'\) hook has its scope set on the entity to be deleted. **Except** when an _Array_ of ids to delete is passed.

```js
blogSchema.pre('delete', function() {
    console.log(this.entityKey); // the datastore entity key to be deleted

    // Access arguments passed
    const args = Array.prototype.slice(arguments);
    console.log(args[0]); // 1234 (from call below)

    // By default the entity data is not present because
    // the entity is *not* fetched from the Datastore.
    // Though you can call "this.datastoreEntity()" here (see the "Entity" section)
    // to fetch the data from the Datastore and process any other logic
    // before resolving the middleware

    // example 1:
    return this.datastoreEntity() // fetch the entity data from the Datastore
                .then((entity) => {
                    // entity is a gstore entity instance
                    // You could delete an associated uploaded image for ex.
                    return myImageHelper.deleteImage(entity.imageIdx)
                                        .then(() => Promise.resolve()); // always resolve empty unless...
                });

    // example 2:
    // ... if you want to override the original argument passed resolve passing a value.
    // Here you would override the id to delete! At your own risk...
    return Promise.resolve({ __override: 1235 });
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

### Dataloader instance

In case you provided a Dataloader instance to a Model.update\(\) call, it will be added to the entity being saved. This means that it is accessible from inside your "pre" save hooks.

```js
function myPreSaveMiddleware() {
    // fetch the BlogPost author detail with the "entity.model()" method
    return this.model('User')
        .get(this.author, null, null, null, { dataloader: this.dataloader })
        .then((user) => {
            this.authorDetails = user;
        });
}
```

### Override parameters

In the rare cases \(maybe during a migration of data\) where you'd need to override the parameters in a "pre" hook, you can resolve your middleware with an object containing an `__override`property.

```js
const userSchema = new gstore.Schema({
    user: { type: 'string' },
    email: { type: 'string', validate: 'isEmail' },
    password: { type: 'string', excludeFromIndexes: true }
});

userSchema.pre('findOne', (...args) => {
    if (args[0].email === 'john@snow.com') {
        return Promise.resolve({ __override: [
            args[0], // we keep the original props|values passed
            ['Dad', 'Targaryen'] // we add an Ancestor to the Query for this user
        ]});
    }
    return Promise.resolve();
});

// ...

// Whenever you will search for this user,
// the Query will occur on the Ancestor ['Dad', 'Targaryen'];
User.findOne({ email: 'john@snow.com' }).then((user) => { ... })
```



