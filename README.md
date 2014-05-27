# Lithium - Error monitoring for Neon

Lithium is a Code monitoring tool for Neon. Its main use case is to detect execution events on the JavaScript code such as Errors.

It monitors the code at the execution level, so it can be used on live code to detect errors, and notify about this errors to another library or the console.

## Installation

Lithium depends on a patched version of neon, (provided on this repo).

```js
<script src="./neon.js"></script>
<script src="./neon.li_patch.js></script>"
<script src="./lithium.js></script>

// place other neon based files here."
```

## Usage

Lithium exposes a set of Engines for the execution phases of code.

- Before execution: This is triggered before the execution of a function.
- Error detection: This is triggered in the case that a function dispatched an error.
- After execution: This is triggered after the function returned the execution control.

All of the engines act as a list of functions that will be executed in the order they were added to the engine pool.

Note that code that was not called by a Lithium monitored code will not detect errors because its not possible to do so. Errors trapped and handled by
your code will not be detected either (this is a JavaScript behavior).

The internal data structure of this engines is a plain Array object so you can modify its values with the vanilla Array methods from JavaScript.

On this example we are going to focus on the Error engine.

```js
/*
data object contents.
data = {
    scope : <<Object>>, //The object where the error was detected
    args : <<Array>>, //The arguments that the function received
    spy : <<Object>>, //The reference to the Lithium Object monitoring this function
    error : <<Error>> //The internal JavaScript Error object
}
*/

Li.Engine.error.push(function (data) {
    console.error(data);
});

```

## Advanced Usage

On this example we are going to get more information out of the error notification on the Error Engine.

We are going to focus on the spy properties.

```js
spy = {
    targetObject : <<Object>>, //The object that holds the function
    methodName : <<String>>, //The property name where the function lives
    originalMethod : <<Function>>, //The original method of the object
    objectHasMethod : <<Boolean>> //Tells if the method actually lives on the object or lives on the prototype chain
}

Li.Engine.error.push(function (data) {
    var errorData = [
        'Lithium Detected an Error on ',
        data.scope.className,
        ' on method  ',
        data.spy.methodName,
        ' with message  ',
        data.error.message
    ];
    console.error(errorDatai.join(' '));
    console.error(data.error.stack);
});
```


## Enabling or Disabling Lithium

Lithium is enabled by default but there are cases where you don't want this.

The first strategy to fully disable it is by running this code after Lithium inclussion.

```js
enableLi = false;
```

If you want to remove Lithium on runtime from a specific object.

```js
object.otherObject.__objectSpy.destroy();
```

## Final notes

Lithium does not require interaction with its code. So there are no methods that you have to run or any instance that you have to create
all its handled by itself.

You dont need to code for lithium, it will monitor if you ask it to do so.
