#Middleware (hooks)

Middleware or 'Hooks' are functions that are executed right before or right after a specific action on an entity.  
Hooks are available for the following methods

- Entity.save() (also executed on Model.**update()**)
- Model.delete()
- Model.findOne()
- On your custom methods

Your hooks **must return a Promise** and you must use the "Promised" version of the methods, **not** the callback.
