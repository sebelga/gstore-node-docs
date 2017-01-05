# Schema methods

## path()
Getter / Setter for schemas paths.

```js
var mySchema = new Schema({name:{type:'string'});

// Getter
mySchema.path('name'); // returns {type:'string'}

// Setter
mySchema.path('email', {type:'string', validate :'isEmail'});

// From a Model
var User = gstore.model('User');

// add new path to User Schema
User.schema.path('age', {type:'number'});

```

## virtual()

Virtuals are properties that are added to the entities at runtime that are not persisted in the Datastore. You can both define a **getter** and a **setter**.

**getter**

```js
var schema = new Schema({
	firstname: {},
	lastname : {}
});

schema.virtual('fullname').get(function() {
	// the scope (this) is the entityData object

	return this.firstname + ' ' + this.lastname;
});

var User = gstore.model('User', schema);

var user   = new User({firstname:'John', lastname:'Snow'});
console.log(user.fullname); // 'John Snow';

/*
* You can also set virtuals to true in plain method to add them to your output object.
*/
var output = user.plain({virtuals:true});

console.log(output.fullname); // 'John Snow';

```

**setter**

```js
var schema = new Schema({
	firstname: {},
	lastname : {}
});

schema.virtual('fullname').set(function(name) {
	var split      = name.split(' ');
	this.firstname = split[0];
	this.lastname  = split[1];
});

var User = gstore.model('User', schema);

var user = new User();
user.set('fullname', 'John Snow');

console.log(user.get('firstname')); // 'John';
console.log(user.get('lastname')); // 'Snow';

// You can save this entity without problem as virtuals are removed from the entity data before saving
user.save(function() {...});

```


