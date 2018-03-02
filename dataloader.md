# Dataloader

[Dataloader](https://github.com/facebook/dataloader) is a utility from Facebook to "reduce requests to the backends via batching and caching".

It combines all individuals loads within a single frame of execution (a single tick of the event loop) and then calls its batch function with all requested keys, making sure we don't hit the backend asking for the same key twice.

Although Dataloader has a memoization cache for all loads which occur in a single request, it is not meant to be used as a cache layer. As it excplicitly says, Dataloader "is a data loading mechanism, and its cache only serves the purpose of not repeatedly loading the same data in the context of a single request to your Application".

You create a new Dataloader instance **for each request**, and pass it to gstore methods as an option property. 
gstore-node provides a **helper** to create a Dataloader instance that works with the Google Datastore. You still need to install the dependency and have it in your package.json (`npm install dataloader --save`)  

Let's see it through some examples.

```js

```