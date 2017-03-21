#Queries

## findOne

Quickly find an entity by passing key/value pairs. You can optionally pass an ancestors array or a namespace.

**@Returns** an gstore entity **instance** of the Model.

```js
User.findOne({prop1:value, prop2:value2}, ancestors /*optional*/, namespace /*optional*/, callback);
```


```js
var User = gstore.model('User');

User.findOne({email:'john@snow.com'}, function(err, entity) {
    if (err) {... deal with error}

    console.log(entity.plain());
    console.log(entity.get('name'));
});

```

If no callback is passed, a **Promise** is returned

```js
User.findOne({email:'john@snow.com'}).then((data) => {
	const entity = data[0];

    console.log(entity.plain());
    console.log(entity.get('name')); // or directly entity.name;
});
```