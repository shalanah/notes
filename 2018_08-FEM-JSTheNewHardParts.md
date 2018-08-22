# JavaScript: The New Hard Parts
Notes and examples from a Front End Masters course.

## TOC
- [JavaScript Code Execution](#javaScript-code-execution)
- [Web Browser APIs](#web-browser-apis)

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
  - __Task queue__ (many names... callback queue/ macro task queue)
    - Queue of functions ready to come back on the call stack
  - __Event loop__ - Can run once the call stack is empty and all our global code is done running


#### Example facade function `setTimeout`
- `setTimeout` is a facade function that starts Web Browser Feature of `Timer`
  - checks when complete (duration)
  - on completion what function to run (when it can on the call stack)

  
