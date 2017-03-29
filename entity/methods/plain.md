# Entity Methods

## plain()

This methods returns the entity **data** and its entity key **id** (int or string)

It accepts one optional **options** argument

```js
entity.plain({
    readAll: true|false,
    virtuals: true|false,
    showKey: true|false,
}):
```

#### options

- **readAll **(default: false)

Outputs **all** the data properties, regardless of the schema "read" parameter.

- **virtuals **(default: false)  

Adds the schema "virtuals" to the output. (see: [schema virtuals](../../schema/schema-methods/virtual.md))

- **showKey**(default: false)

Adds a "__key" property to the entity data with the complete Key from the Datastore.