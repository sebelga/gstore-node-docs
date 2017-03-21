# gstore Methods

## save()

gstore has a global method "save" that is an alias of the original Datastore save() method, with the exception that you can pass it an Entity **instance** or an **\<Array\>** of entities instances and it will first convert them to the correct Datastore format before saving.  


`gstore.save(entity | entity[], transaction /*optional*/)`



**Note**: The entities can be of **any** kind. You can concat several arrays of queries from different Models and then save them all at once with this method.

Example:

```js
const query = BlogModel.query().limit(20);
query.run({ format: gstore.Queries.formats.ENTITY })
	  .then((result) => {
	  	const entities = result[0].entities;
	  	
	  	// entities are gstore instances, you can manipulate them
	  	// and then save them by calling:

	  	gstore.save(entities).then(() => {
	  		...
	  	});
	  });

/*
 * From inside a transaction
 */
const transaction = gstore.transaction();

transaction.run().then(() => {
	const query = BlogModel.query(null, transaction).limit(20);
	query.run().then((result) => {
		const entities = result[0].entities;
		
		// Call the global save method passing the transaction.
		gstore.save(entities, transaction).then( ... );
	});
})

```
