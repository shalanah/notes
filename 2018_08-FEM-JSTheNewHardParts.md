# JavaScript: The New Hard Parts
Notes and examples from a Front End Masters course.

## TOC
- [JavaScript Code Execution](#javaScript-code-execution)
- [Web Browser APIs](#web-browser-apisnode-background-threads)
- [Intro Promises](#intro-promises)
- [Promises & Microtask Queue](#promises--microtask-queue)

## JavaScript Code Execution
### Global execution context
When we start running code we create the global execution context and it has two main parts:
- __Thread of execution__ - line by line, top to bottom
- __Global variable environment (VE)__ - live memory of variables with data

We also have local execution contexts and local variable environments, happens when we run functions.

### Other notes...
- __Parameter__ vs __Argument__
  - Parameter - variable in the declaration of a function
  - Argument - value that gets passed in (arguments are "actual")
- JS is single threaded
- __Call stack__ - keeps track of where to run code and where we need exit to
  - Push adds to a stack
  - Pop takes off

## Web Browser APIs/Node background threads
Browser features are needed to have asynchronicity, JS doesn't have them baked in because JS is single threaded.
- __Facade functions__ - looks like JS but really browser features, use JS to interface with these browser features
- __Call stack implications__ (for deferred browser features)
  - __Task queue__ (many names... callback queue/ microtask queue - JS engine feature)
    - Queue of functions ready to come back on the call stack
  - __Event loop__ - Checking to see when queue functions can come back on the stack.
  - Deferred func can run once the call stack is empty and all our global code is done running


#### Example facade function `setTimeout`
- `setTimeout` is a facade function that starts Web Browser Feature of `Timer`
  - checks when complete (duration)
  - on completion what function to run (when it can on the call stack)

### Other notes
- __Queue__ vs __Stack__
  - Queue - First-In-First-Out (FIFO) or Last-In-Last-Out (LILO). Like in broomball :), a person getting into line is "enqueued" and the person getting out of the line is "dequeued"
  - Stack - Last-In-First-Out (LIFO). Add is "pushing", and removing is "popping"

#### Some visuals from Stack Overflow
##### Stack
![](https://i.stack.imgur.com/fUtR1.png)
##### Queue
![](https://i.stack.imgur.com/CqutZ.png)

## Intro Promises
Readability enchancer called Promises
- Special objects (Promise object) built into JavaScript that get returned immediately when we make a call to a web browser API/feature (e.g. Fetch) that's set up to return promises (not all are)
- Act as placeholders for the data we hope to get back from the web browser feature's background work
- Attach functionality we want to defer running until the background work is done (.then method)
- Promise objects will automatically trigger that functionality to run

### Example with `fetch`
```js
function display (data) {
  console.log(data)
}
const futureData = fetch('url')
futureData.then(display)
console.log('me first')
```
- In memory (VE), declaring `display` and store func definition
- In memory (VE), declaring `futureData` and its value will be the  return value of execution of `fetch` but starts out as undefined

`fetch` is "Two pronged" :)
- Immediately return out the __Promise Object__ and assigned to `futureData`...
```js
 {
   value: undefined, // not defined initially
   onFullfillment: [] // hidden property array of func to run once the value is filled in
 }
```
- `fetch` is a facade function which starts the Web Browser Feature `XMLHttpRequest`
  - `xhr` needs...
  - Needs url & path
  - Method `get`
  - On completion...
    - Assign the response data to our Promise Object (`futureData`) value property
    - Then it will trigger the functions in our onFullfillmnet array
- `.then` property will store function on our onFullfillment array to be triggered when our Promise object value has been updated
- Add `display` to our onFullfillment array
- Our `console.log('Me first')` runs
- Once data comes back from xhr and our promise object value is filled in... (Microtask queue) trigger running display with promise object value
## Promises & Microtask Queue
What happens first? How do multiple deferred func get run in JS?
```js
setTimeout(printHello, 0) // (1) When done... adds printHello to the Callback Queue. Event Loop starts checking when it can be added back on to the Call Stack

const futureData = fetch('url here') 
futureData.then(display) // (2) When done... Promise object value gets updated by the background Web Browser Feature which DOESN'T go into the Callback Queue but goes INTO the MICROTASK queue

blockFor1000ms() 

console.log('Me first')

// Which queue wins? Event loop prioritizes the Microtask queue
// - display function wins (runs)
// - printHello runs
```
- Callback/Task Queue
- Microtask/Job Queue (event loop prioritizes this queue)
- Need to look things up to figure out which queue deferred fuctions go to
- Technically the status of the Promise Object that triggers the onFullfillment array functions (`pending`, `fullfilled`, `rejected` -> triggers onRejection `.catch` or second function parameter on `.then`)
- Spec... so yeah browsers sometimes do what they want (but modern should be pretty consistent)


  
