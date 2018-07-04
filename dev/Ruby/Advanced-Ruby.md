Advanced Ruby Features
-----------------------
## Metaprogramming
### Dynamic Dispatch
In static languagesthe comiler requiresyou to define all the methods upfront.
In dynamic language, such as Python and Rub, methods don't have to be predefined. They need to only be found when invoked.

Dynamic method calling, a.k.a."Dynamic Dispatch":
  + can decide at runtime which methods to call
  + the code does not need to find untill runtime which method it needs to call

Methods can be called dynamicallyusing a string or a symbo, not using the dot notation.

### Dynamic Methods
Definingmethods dynamicallycan dramatically reducethe amount of codethat needs to be written.

No need to write repetitive methods.

Dynamic methods example: ![dynamic methods image](./dynamic-methods "Dynamic methods")

### Ghost Methods

+ Ruby looks for the method invoked in the class to which it belongs
+ Then goes up the ancestors tree (classes, modules)
+ If it still does not find the method, it calls the `method_missing`method
+ The default `method_missing` implementation throws a NoMethodError
+ We can override `method_missing` method in our class
So:
+ `method_missing` gives you the power to fake the methods
+ it is called ghots methods because the methodsdo not really exist
+ Ruby uses `method_missing`

Ghost methods example: ![ghost methods image](./ghost-methods "Ghost methods")
