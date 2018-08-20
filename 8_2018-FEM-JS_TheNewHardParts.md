# JavaScript: The New Hard Parts
Notes and examples from a Front End Masters course.

## TOC
- [JavaScript Code Execution](#javaScript-code-execution)

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
- Call stack - keeps track of where to run code and where we need exit to
  - Push adds to a stack
  - Pop takes off

## Introducing Asynchronicity
