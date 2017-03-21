#Queries

## findAround

Find entities before or after an entity based on a property and a value.

```js
MyModel.findAround(
    /* {string}. -- The property to look around */
    <property>,
    /* {string} -- The property value */
    <value>,
    /* {object} -- { before|after: limit } */
    <options>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

The **options** argument is an object with *either* a "before" or an "after" key with the corresponding limit of entities to retrieve.

Example:
```js
// Find the next 20 post after march 1st
BlogPost.findAround('publishedOn', '2016-03-01', { after: 20 }).then((response) => {
    const entities = response[0];
});

// Find 10 users with the lastname 
User.findAround('lastname', 'Jagger', { before: 10 }).then( ... );

// with a callback
BlogPost.findAround('publishedOn', '2016-03-01', {after:20}, function(err, entities){
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
