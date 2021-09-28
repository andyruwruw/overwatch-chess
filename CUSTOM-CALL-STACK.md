# Overwatch Workshop: Custom Call Stack

# Table of Contents
- Background
- Implementation
- Usage

# Background

# Implementation

# Usage

**Declare variable**:

```
// let variable1 = 0;
Global.Stack1[Global.Stack1[1]] = Custom String("variable1");
Global.Stack1[Global.Stack1[1] + 1] = 0;
Global.Stack1[1] += 2;
```

**Accessing Variable Value in Scope**:

```
// variable1
Global.Stack[Index Of Array Value(Array Slice(Global.Stack, Global.Stack[0], Global.Stack[1] - Global.Stack[0]), Custom String("variable1")) + 1 + Global.Stack[0]]
```

**Accessing Global Variable Value**:

```
// variable1
Global.Stack[Index Of Array Value(Global.Stack, Custom String("variable1") + 1]
```

**For loop**:

```
// let i = 0;
Global.Stack[Global.Stack[1]] = Custom String("i");
Global.Stack[Global.Stack[1] + 1] = 0;
Global.Stack[1] += 2;

// while (i < 5)
While(Global.Stack1[Index Of Array Value(Array Slice(Global.Stack1, Global.Stack1[0], Global.Stack1[1] - Global.Stack1[0]), Custom String("i")) + 1 + Global.Stack1[0]] < 5);
	...
	// i += 1;
	Global.Stack1[Index Of Array Value(Array Slice(Global.Stack1, Global.Stack1[0], Global.Stack1[1] - Global.Stack1[0]), Custom String("i")) + 1 + Global.Stack1[0]] += 1;
End;
```

**Calling subroutines as functions with no parameters**:
```
rule("Previous Action")
{
	...

	actions
	{
		...

		// Return Address
		Global.Stack1[Global.Stack1[1]] = Global.Stack1[0];
		Global.Stack1[1] += 1;

		// SubroutineName();
		Call Subroutine(SubroutineName);

		...
	}
}

rule("Called Subroutine")
{
	event
	{
		Subroutine;
		SubroutineName;
	}

	...

	actions
	{
		// Sets Local Scope (FramePointer) with 0 Parameters
		Global.Stack1[0] = Global.Stack1[1];

		...

		// Rollback stack pointer
		Global.Stack1[1] = Global.Stack1[0] - 1;
		// Set scope to previous (FramePointer)
		Global.Stack1[0] = Global.Stack1[Global.Stack1[1]];
	}
}
```

**Calling subroutines as functions with parameters**:
```
rule("Previous Action")
{
	...

	actions
	{
		...

		// Return Address
		Global.Stack1[Global.Stack1[1]] = Global.Stack1[0];
		Global.Stack1[1] += 1;

		// parameter: parameter1 = 0;
		Global.Stack1[Global.Stack1[1]] = Custom String("parameter1");
		Global.Stack1[Global.Stack1[1] + 1] = 0;
		Global.Stack1[1] += 2;

		// parameter: parameter2 = 0;
		Global.Stack1[Global.Stack1[1]] = Custom String("parameter2");
		Global.Stack1[Global.Stack1[1] + 1] = 0;
		Global.Stack1[1] += 2;

		// parameter: parameter3 = 0;
		Global.Stack1[Global.Stack1[1]] = Custom String("parameter3");
		Global.Stack1[Global.Stack1[1] + 1] = 0;
		Global.Stack1[1] += 2;

		// SubroutineName(parameter1, parameter2, parameter3);
		Call Subroutine(SubroutineName);

		...
	}
}

rule("Called Subroutine")
{
	event
	{
		Subroutine;
		SubroutineName;
	}

	...

	actions
	{
		// Sets Local Scope (FramePointer) with 3 Parameters
		Global.Stack1[0] = Global.Stack1[1] - (3 * 2);
		
		...

		// Rollback stack pointer
		Global.Stack1[1] = Global.Stack1[0] - 1;
		// Set scope to previous (FramePointer)
		Global.Stack1[0] = Global.Stack1[Global.Stack1[1]];
	}
}
```
