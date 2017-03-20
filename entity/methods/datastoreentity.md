# Entity methods

## datastoreEntity()

In case you need at any moment to fetch the entity **data** from Goolge Datastore, this method will do just that right on the entity instance.

```js
const User = require('user.model');
const user = new User({ name:'John' });

// with a Promise...
User.update(123, { email: 'john@snow.com' })
    .then((reponse) => {
        const entity = response[0];
        entity.datastoreEntity().then((response) => {
            const datastoreEntity = response[0];
            console.log(datastoreEntity.firstname); // 'John'
        });
});

// with a callback
User.update(123, { email: 'john@snow.com' }, function(err, entity) {
    entity.datastoreEntity(function(err, datastoreEntity){
        console.log(datastoreEntity.firstname); // 'John'
    });
});

```
