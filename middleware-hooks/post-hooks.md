# Post hooks

## Post hooks

"Post" middelwares are defined the same way as "pre" middlewares. The main difference is that if you reject the Promise of your middleware because an error occured, the original method still resolves and a **Symbol** is added to the response containing the post hooks error\(s\).

```javascript
// user.model.js

const schema = new Schema({ username: { ... }, ... });

// add "post" save middleware
schema.post('save', function postSave(){
    return new Promise((resolve, reject) => {
        const email = this.email;

        // ... do anything needed, maybe send an email?

        if (someError) {
            // If there is any error reject the Promise
            return reject({ code: 500, message: 'Houston something went wrong.' });
        }
    });
});

// ....

// user.controller.js

const { instances } = require('gstore-node');
const User = require('./user.model');

const gstore = instances.get('default');
const user = new User({ name: 'John', email: 'john@snow.com' });

user.save().then((entity) => {
    // You should do this check if you have post hooks that can fail
    if (entity[gstore.ERR_HOOKS]) {
        console.log(entity[gstore.ERR_HOOKS][0].message); // 'Houston something went wrong.'
    }
    ...    
});
```

**Note** The response from a "post" hook on **delete\(\)** contains the key\(s\) of the entitie\(s\) deleted.

```javascript
// key can be one Key or an Array of Keys deleted
userSchema.post('delete', function postDelete({ key }){
    ...
    return Promise.resolve();
});
```

You can also pass an **Array** of middleware to execute

```javascript
function middleware1() {
    return Promise.resolve();
}

function middleware2() {
    return Promise.resolve();
}

userSchema.post('save', [middleware1, middleware2]);
```

### Transactions and Post Hooks

When you save or delete an entity from inside a transaction, gstore adds an **execPostHooks\(\)** method to the transaction instance.  
If the transaction succeeds and you have any post\('save'\) or post\('delete'\) hooks on any of the entities modified during the transaction you need to call this method to execute them.

```javascript
const transaction = gstore.transaction();

transaction.run().then(() => {
    const user = new User({ name: 'john' });

    user.save(transaction);
    BlogPost.delete(123, null, null, transaction);

    transaction.commit()
        .then((data) => {
            transaction.execPostHooks() // execute "post" hooks
                .then(() => {
                    const apiResponse = data[0];
                    // all done!
                });
            });
});
```

