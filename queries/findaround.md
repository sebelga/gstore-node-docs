#Queries

## findAround

Find entities before or after an entity based on a property and a value.


**options** is an object that contains *either* a "before" or an "after" key with the corresponding limit of entities to retrieve.


`Model.findAround(property, value, settings, callback)`



```js
// Find the next 20 post after march 1st
BlogPost.findAround('publishedOn', '2016-03-01', {after:20}, function(err, entities){
   ...
});

// Find 10 users before Mick Jagger
User.findAround('lastname', 'Jagger', {before:10}, function(err, entities){
   ...
});

```

If no callback is passed, a **Promise** is returned

```js
BlogPost.findAround('publishedOn', '2016-03-01', {after:20}).then((data) => {
	const entities = data[0];
   ...
});
```

**Additional settings**

- readAll {boolean} true | false
- format {string} gstore.Queries.formats.JSON (default) | gstore.Queries.formats.ENTITY

```js
BlogPost.findAround('publishedOn',
						'2016-03-01',
						{after:20, readAll: true, format: gstore.Queries.formats.ENTITY})
		.then((data) => {
			const entities = data[0];
		   ...
		});
```
