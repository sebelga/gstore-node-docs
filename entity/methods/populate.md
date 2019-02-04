# populate\(\)

This methods allows you to fetch all the **references** entities declared on your Schema.

It accepts two optional arguments:

* `references` `(string or Array<string>)` One or multiple references to fetch \(e.g. 'author' or \['author', 'address'\]\)
* `properties` `(string or Array<string>)` The properties to return from the fetched reference entities.

```javascript
entity.populate('user', 'email')
    .populate('address') // Calls can be chained
    .then(() => {
        // entity.user.email has been fetched
        // entity.address will has been fetched
    });
```

For a detailed explanation on how `populate()` works, [refer to the documentation](../../populate.md).

