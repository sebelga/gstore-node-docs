#Queries

## deleteAll

If you need to delete all the entities of a Model, this shortcut query will do just that.
It queries your entity Kind by batches of 500 (maximum set by the Datastore) and delete them.

Disclaimer: this shortcut query hasn't been tested on a very large set of entities so please report any issue you might find.

```js
BlogPost.deleteAll(ancestors /*optional*/, namespace /*optional*/, callback)
```


```js
BlogPost.deleteAll(function(err, result){
    if (err) {// deal with err}

    console.log(result.message);
});

// With ancestors path and namespace
BlogPost.deleteAll(['Grandpa', 1234, 'Dad', 'keyname'], 'com.new-domain.dev', function(err) {...});
```

If no callback is passed, a **Promise** is returned

```js
BlogPost.deleteAll(['Grandpa', 1234, 'Dad', 'keyname'], 'com.new-domain.dev').then(() => {
	...
});
```
