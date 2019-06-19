# Four Semesters of Computer Science in 5 Hours
- Frontend Masters w/Brian Holt

## Intro
- Good book (free to download book): Cormen's Intro to Algorithms
- "Computer Science... is a science of tradeoffs"
  - ie. fast/take less memory/more human readable
  - Brian leans towards more human readable/mainability but this workshop will probably go the other way of less readable :)

## Big O
- Analyze how efficient an algorithm is
- Care about orders of magnitude diff in time
- Big O really is big picture... not about the details... don't care about small changes in time
- bigocheatsheet.com
  - Spacial complexity: sorting the array itself and not creating new arrays (O(1))... memory stuff :)

### Example: 3x^2 + x + 1
- Showed with varying inputs. 
- x^2 is what has the most impact on our outputs
- Can ignore those smaller parts (x + 1)
- Big O: **O(n^2)**

### Examples with code:
```js
const crossAdd = input => {
    var answer = []
    for (var i = 0; i < input.length; i ++){
        var goingUp = input[i]
        var goingDown = input[input.length - 1 - i]
        answer.push(goingUp + goingDown)
    }
    return answer
}
```
- **O(n)** since we go over each input 1x (broad strokes!)

```js
const find = (needle, haystack) => {
    for (int i = 0; i < haystack.length; i++) {
        if (haystack[i] === needle) return true
    }
}
```
- **O(n)** look at worst case scenario, potential to loop over everything

```js
const makeTuples = input => {
    var answer = []
    for (var i = 0; i < input.length; i++) {
        for (var j = 0; j < input.length; j++) {
            answer.push([input[i], input[j]])
        }
    }
    return answer
}
```
- **O(n^2)** going over each input n times making it squared

### Tips
- NO LOOPS: Constant time **O(1)**
- 1 loop: **O(n)**
- 2 loops: **O(n^2)**
- 3 loops: **O(n^3)** etc...
- Good question was what if you have adjacent loops then it would be still **O(n)**
- Divide-and-conquer **O(log n)** (often recursive, merge, sort, quick-sort, trees)

## Recursion
- When you define something in terms of itself
- Shows example of Sierpiński triangle
- Short to write but hard to write :)
- Each level of recursion can have a state which is super useful
- Does have a cost (not free)
  - Adds more func to the stack
  - If you can change to iterative, that can carry less cost

### Examples
```js
const basicRecursion = (max, current) => {
    if (current > max) return // Base case: Write first! Stop recursion, prevent stack overflows
    console.log(current)
    basicRecursion(max, current + 1)
}
basicRecursion(5,1)
// Output
// 1
// 2
// 3
// 4
// 5

// Stack...
// 5. (5, 6) // then start popping off stack
// 4. (5, 5)
// 3. (5, 4)
// 2. (5, 3)
// 1. (5, 2) 
// 0. (5, 1)
```
- Not a worthwhile func, should be done with recursion instead

```js
const fibonacci = n => {
    if (n <= 2) return 1 // BASE CASE... be aggressive... B-E aggressive
    else return fibonacci(n - 1) + fibonacci(n - 2)
}
fibonacci(5)
// fib(4) + fib(3)
// (fib(3) + fib(2)) + (fib(2) + fib(1))
// ((fib(2) + fib(1)) + 1) + (1 + 1)
// ((1 + 1 + 1) + 1 + 1)
// 5
// Pretty... inefficient for large numbs b/c you'll be adding up a bunch of 1s
```

```js
const factorial = n => {
  if (n < 2) return 1
  else return n * factorial(n - 1)
}
```

## Sorting Algorithms
### Bubble Sort
- Not useful... don't use in production
- Good starting point however
- Animation of it working looks like numbers "bubble" up
- Compare 2 numbers at a time then swap if out of order
- Big O:
  - Inner loop checking indexes
  - Outter loop checking to see if anything swapped
  - **O(n^2)**
```
// How it works example
// [5,7,6,4]
// First it, go through + swap if out of order
// - 5 & 7 no swap [5,7,6,4]
// - 7 & 6 swap [5,6,7,4]
// - 7 & 4 swap [5,6,4,7]
// End up with [5,6,4,7]
// Next pass
// - 5 & 6 no
// - 6 & 4 swap [5,4,6,7]
// - 6 & 7 no
// End up with [5,4,6,7]
// Next pass
// - 5 & 4 swap [4,5,6,7]
// - 5 & 6 no
// - 6 & 7 no
// End up with [4,5,6,7]
// Next pass
// - Double checks
// Done [4,5,6,7]
```

#### Code
```js
// My attempt
const bubbleSort = arr => {
  let sorted = [...arr]
  let didSwap = true // just to get started
  const len = arr.length
  while (didSwap) {
    didSwap = false
    for (let i = 0; i < len - 1; i ++) {
      const a = sorted[i]
      const b = sorted[i + 1]
      if (b < a) {
        sorted[i] = b
        sorted[i + 1] = a
        didSwap = true
      }
    }
  }
  return sorted
}

// Brian's
// - Diff he mutates his array
// - I've never used do/while... The do/while statement creates a loop that executes a block of code once, before checking if the condition is true, then it will repeat the loop as long as the condition is true.
const bubbleSort = nums => {
  let swapped = false
  do {
    swapped = false
    for (let i = 0; i < nums.length; i ++) {
      if (nums[i] > nums[i + 1]) {
        const temp = nums[i] // I like this... less storage
        nums[i] = nums[i+1]
        nums[i+1] = temp
        swapped = true
      }
    }
  } while (swapped)
}
```
### Insertion Sort
- Great for things that are kind of sorted, or close to sorted
- Falls a part if array is not sorted AT ALL
- Big O:
  - **O(n^2)**
  - Still better than bubble sort due to better coefficients
  - Inner loop + outer loop still

#### How it works
- [5,3,6]
- sub array starts at first index [5]
  - sorted list of one
- grab things from unsorted (`3`) and place them correctly in the sorted
- Place 3 in relation to 5
- [3,5,6]
- Look at 6 does it go after 3? after 5?
- Place after 5

#### Code
```js
const insertionSort = arr => {
  for (let i = 1, len = arr.length; i < len; i++) { // item being sorted
     for (let j = 0; j < i; j++) { // "sub" array comparisions 
       if (arr[i] < arr[j]) {
         const temp = arr.splice(i, 1)
         arr.splice(j, 0, temp[0])
       }
     }
  }
 return arr
}
```









