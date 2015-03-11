# Promise polyfill for [Wakanda](http://wakanda.org)

![Bluebird Icon](./icons/bluebird.png) 
![Promise Icon](./icons/then.png) 

The `Bluebird` "custom widget" Polyfill ECMAScript 6 Promise API in Wakanda Pages using a core build of the [Bluebird](https://github.com/petkaantonov/bluebird) library.

To take even more advantages of Promises with Wakanda, feel free to also look at the **[WPromise](https://github.com/AMorgaut/WPromises)** custom widget that _automatically transforms native Wakanda APIs into promisified version_.

## Why Promises

With promises, it becomes easy:

* to add one or more success and/or error handler of an asynchrnous call at any moment, even after the call was done
* to chain multiple asynchronous calls
* to parallelise multiple asynchronous calls and do something once all of them are ready 

## How to Install

You can install the `Bluebird` widget by using the [Add-ons Extension](http://doc.wakanda.org/WakandaStudio/help/Title/en/page4263.html "Add-ons Extension"). 

For more information, refer to the [Installing a Custom Widget](http://doc.wakanda.org/WakandaStudio/help/Title/en/page3869.html#1056003 "Installing a Custom Widget") manual.

## How to Use

Here a very simple example:
```javascript
(new Promise(function (resolve) {
	navigator.geolocation.getCurrentPosition(resolve);
}).then(function (position) {
	// here we get the current client position
}).catch(function (error) {
	// geolocation API is not available
});
```
### Initialisation

1. Drag & drop the `WPromise` widget on your Wakanda Page. It will automatically Promisify the supported Wakanda Framework APIs.
2. Or define it as an external widget in from another custom widget package.json file

### API Basics

#### Promise()

The `Promise` constructor use what is called a `resolver` which receive 2 callbacks: 

* `resolve(value)` 
* and `reject(error)`. 

You can do whatever asynchronous call in this resolver, and then choose which callback to call when you are ready. 

The promise instance is a classic JavaScript object that can be used immediatly, sent to other functions as parameter, 
or store as a third party object property for later use.

The promise instance has a state that can be:

* `pending`: its initial state when it is created
* `fulfilled`: its state once the `resolve()` callback has been used
* `rejected`: its state once the `reject()` callback has been used

Once the promise change its state to fulfilled or rejected, it can not change again, it is immutable. 
It also means that once resolve() or reject() is called, next calls to resolve ro reject have not effect.

The promise instance provides 2 methods:

* `then(handler)`: the handler will be called if the promise becomes `fulfilled` (immediatly if it is aldready)
* `catch(handler)`: the handler will be called if the promise becomes `rejected` (immediatly if it is aldready) 

They can of course be called whenever you want, and as many times you want.

_Important Notes_

* If an exception is thrown in the resolver, it is intercepted and the promise automatically becomes `rejected`
* then() and catch() return new promises which will be resolved with their returned value
* if an exception is thrown in a then() or catch() handler, it is also intercepted and the returned promise automatically becomes `rejected`

#### Promise helpers

The Promise constructor also comes with its own set of methods:

* `Promise.resolve(value)`: return a resolved promise
* `Promise.reject(error)`: return a rejected promise
* `Promise.all(promises)`: return a promise that will be resolved once all promises are resolved

### Advanced Example

```javascript
// get the client geolocalisation
var myGeolocationPromise = new Promise(function geolocationResolver(resolve) {
	navigator.geolocation.getCurrentPosition(resolve);
});

// do a Wakanda Query Call
var myDataproviderPromise = new Promise(function dataproviderResolver(resolve, reject) {
	ds.myClass.query('age > :1', {
        params: ['25'],
        onSuccess: resolve, onError: reject
    });
});

// do chained Wakanda RPC Calls
var myChainedRpcPromise = new Promise(function rpcResolver(resolve, reject) {
	myRpcModule.myMethodAsync({
        params: ['foo', 'bar'],
        onSuccess: resolve, onError: reject
    });
}).then(function (result1) {
    retun new Promise(function secondRpcResolver(resolve, reject) {
		myRpcModule.mySecondMethodAsync({
    	    params: ['baz'],
        	onSuccess: function (result2) {
				resolve([result1, result2]);
			},
			onError: reject
    	});
	);
});

Promise.all([myGeolocationPromise, myChainedRpcPromise, myDataproviderPromise])
.then(function (results) {
    // do whatever when all those async results are available
})
.catch(function (error) {
	// manage whatever error that might have happened in any of those async calls
	// or then() handlers
});
```

## References

### Standard

* [ECMAScript 2015 Promises](http://people.mozilla.org/~jorendorff/es6-draft.html#sec-promise-constructor)

### Tutorials

* [Embrassing Promises](http://javascriptplayground.com/blog/2015/02/promises/)
* [HTML5 Rocks: Promises](http://www.html5rocks.com/en/tutorials/es6/promises/)
* [Dr Axel Rauschmayer Blog: Promises](http://www.2ality.com/2014/10/es6-promises-api.html)
* [Promise Patterns](https://www.promisejs.org/patterns/)

## About Wakanda Custom Widgets

For more information about creating a custom widget, refer to the [Widgets v2 Creating a Widget](http://doc.wakanda.org/Wakanda/help/Title/en/page3849.html "Widgets v2 Creating a Widget") manual.


## License

Copyright 2015 Alexandre Morgaut

MIT License, as for the original Bluebird Library it is based on
