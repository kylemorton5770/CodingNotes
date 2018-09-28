#NodeJS Notes 
---------------------

##KEY Feature 
-Asynchronous APIs w/out ever needing to deal w/ threads.
--allows multiple requests to come in w/out freezing your API w/ a lack of threads since Node is single-threaded.



##Running a script file - Module/Exports

//node wraps your code in the below function -> this is why you can use exports, module, require right out of the box!!
````
//function (exports, module, require, __filename, __dirname) {

let g = 1; //local variable inside the LOCAL wrapping function, NOT GLOBAL

console.log(arguments);

//both are valid (exports is just a ref to module.exports)
exports.a = 42; 
module.exports.b = 41; 

// return module.exports; //implied return for the wrapping function (why you can do exports in your file)

//}

````

__Module.exports__ is just your local API that you're setting up. You can add to it (or even reassign) it if needed


## Global

-stores global variables for use in any module. 
-bad practice to store variables here!



##__Event Loop__
-what node uses to process async actions and interface them for you (so you don't have to deal w/ threads).

ex. timer -> Event Loop will monitor the timer and call your callback when it's needed

__On unhandled exception -> Event Loop will stop and app will crash__


## Node Clusters
-monitors nodeJS processes for crash
-how prod will continue to even if one node process errors out (1 for each CPU core)
-will switch to another process on error (or restart single process if single core)

## 'Error-First' callback pattern
-Node normally uses the callback parameter convention of (error, data1, data2, ...)

```
fs.readfile(__filename, function(err, data) {
    console.log("this is error first callback!');
});
```

### 'Pyramid of Doom' callback-nesting & Node's Async Pattern
-instead of nesting callbacks (which are hard to read/maintain), use the promise pattern of promise().then() etc.
-you can convert existing callbacks into promises using the util.promisify function. 

```
 
 //wrap function in promises
 const readfile = util.promisify(fs._readfile);

//creating an async function to await readFile promise
async function main() {
    const data = await readFile(__filename);
    console.log('file data is ', data);

}

main();

```

-promises are the standard as opposed to callbacks.

### Event Emmitters

```
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

myEmitter.on('TEST', () => {
    console.log('event emitted!');
});

myEmmiter.emit('TEST');

```

-with event loop to delay execution

```
const EventEmitter = require('events');

const myEmitter = new EventEmitter();

//this places the callback on the event-loop so it gets called on every tick of the loop (so after all other lines have finished)
setImmediate(() => {
    myEmmiter.emit('TEST');
})

myEmitter.on('TEST', () => {
    console.log('event emitted!');
});


```