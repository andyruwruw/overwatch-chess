# Overwatch Chess

# Custom Call Stack

Overwatch Workshop limits us to 26 global variables and 26 player variables per player. Because of these limitations we cannot create and use scoped variables.

```
variables
{
  global:
	0: variable1
    ...
    25: variable26
  player:
    0: variable1
	...
    25: variable26
}
```

The best way past this limitation is to turn global variables into arrays, which allow us to expand past 26 at the cost of one global variable. Basically wishing for more wishes with a wish.

```
variables
{
  global:
	0: variable1 = [ variable27, variable28, variable29... ]
    ...
    25: variable26
  player:
    0: variable1 = [ variable27, variable28, variable29... ]
	...
    25: variable26
}
```

## Call Stack

We can even go as far as to create our own version of a [call stack](https://en.wikipedia.org/wiki/Call_stack). This will allow us to not only declare local variables, but pass parameters to [subroutines](#subroutines-as-functions).

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d3/Call_stack_layout.svg/342px-Call_stack_layout.svg.png">
</p>

We can use another variable as our **stack pointer** to manage where the next item can be inserted, and **frame pointer** to manage where the local scope begins.

While we have the ability to append items, this gives us further control to override old variables.

Making the frame pointer an array allows us to combine scope with return address. We can simply pop the last number off and return to the number prior.

```
variables
{
  global:
	0: Stack = [  ]
    1: StackPointer = 0
    2: FramePointer = [ 0 ]
    ...
}
```

As long as we don't end up with a recursive mess, the stack shouldn't get to large.

## Variable Names

Managing the index of all our variables would be time consuming and not allow us to dynamically use the stack.

Instead, we can maintain a separate global variable as an array that contains the names of each variable. From this, we can find the index of the variable by name, and access it in our stack.

```
variables
{
  global:
	0: Stack = [ 0 ]
    1: StackLabels = [ Custom String("variable1") ]
    2: StackPointer = 1
    3: FramePointer = [ 0 ]
}
```

Usage wise it's a bit verbose, but gives us a lot more familiarity with the way we code.

## Call Stack Usage

**Declaring a variable**:
```
Global.Stack[Global.StackPointer] = 32;
Global.StackLabels[Global.StackPointer] = Custom String("numPieces");
Global.StackPointer += 1;
```

### Without Worrying about Variable Name Conflicts

*These methods search the entire stack for variable names, and return the first result. Should two variables share the same name, the first will only be returned.*

**Accessing a variable's value**:
```
Global.Stack[Index Of Array Value(Global.StackLabels, Custom String("numPieces"))]
```

**Reassigning a variable's value**:
```
Global.Stack[Index Of Array Value(Global.StackLabels, Custom String("numPieces"))] = 30;
```

### Avoiding Variable Name Conflicts

*These methods limit their search to between the FramePointer and the StackPointer to only return the results from the current scope. Should variables share the same name, the variable within the current scope will be returned.*

**Accessing a variable's value**:
```
Global.Stack[Index Of Array Value(Array Slice(Global.StackLabels, Last Of(Global.FramePointer), Global.StackPointer - Last Of(Global.FramePointer)), Custom String("numPieces")) + Last Of(Global.FramePointer)]
```

**Reassigning a variable's value**:
```
Global.Stack[Index Of Array Value(Array Slice(Global.StackLabels, Last Of(Global.FramePointer), Global.StackPointer - Last Of(Global.FramePointer)), Custom String("numPieces")) + Last Of(Global.FramePointer)] = 30;
```

### Subroutines

**Subroutine Internal Scope**:
```
// Start of Subroutine: New Scope
Modify Global Variable(FramePointer, Append To Array, Global.StackPointer);
...
// End of Subroutine: Return Address
Modify Global Variable(FramePointer, Remove From Array By Index, Count Of(Global.FramePointer) - 1);
```

# Subroutines as Functions

Rules in Overwatch Workshop can only be triggered by a set of events

- Ongoing - Global
- Ongoing - Each Player
- Player Earned Elimination
- Player Deal