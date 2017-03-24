#Queries

## findAround

Find entities before or after an entity based on a property and a value.

```js
MyModel.findAround(
    /* {string}. -- The property to look around */
    <property>,
    /* {string} -- The property value */
    <value>,
    /* {object} -- { before|after: limit, readAll: boolean, format: string } */
    <options>,
    /* {function} -- optional. The callback, if not passed a Promise is returned */
    <callback>
)
```

The **options** argument is an object with *either* a "before" or an "after" key with the corresponding limit of entities to retrieve.

Examples:
```js
// Find the next 20 post after march 1st
BlogPost.findAround('publishedOn', '2016-03-01', { after: 20 })
        .then((entities) => {
            ...
        });

// Find 10 users with the lastname coming before 'Jagger'
User.findAround('lastname', 'Jagger', { before: 10 })
    .then( ... );

// with a callback
BlogPost.findAround('publishedOn', '2016-03-01', {after:20}, function onEntities(err, entities){
   ...
});
```

**Additional options**

- **readAll** {boolean} true | false -- will output all the properties regardless of the *read* parameter in the Schema
- **format** {string} "JSON" (default) | "ENTITY"

```js
const options = { after: 20, readAll: true, format: "ENTITY" };
BlogPost.findAround('publishedOn', '2016-03-01', options)
        .then((entities) => {
            ...
        });
```
