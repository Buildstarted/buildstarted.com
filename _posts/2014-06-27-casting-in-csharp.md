---
layout: post
title: Direct casting vs as casting
---

There are two primary ways to cast in C#, Direct casting and 'as' casting.

###Explicit/Implicit casting
Here are some examples of Direct/Implicit casting

    byte source = 1;
	int value = source;
    
    object source = 1; //this assignment defaults to an int
    int value = (int)source;
    /* or */
	byte value = (byte)source;

    
Implicit casts can be used when converting from one type to another without any loss of information. In this example from a byte to a higher order numeric value. `Byte` to `Int32`, `Int32` to `Double`. You cannot, however, cast down from `Double` to `Byte` without doing an explicit cast. 

The second example shows an explicit cast from an integer to a byte. The explicit cast here is required because there could be a loss of information going from a larger `int` (4 bytes) to a `byte` (1 byte).

###Casting using the `as` operator
The types must be nullable or reference objects in this case.

    object animal = new Dog();
    var value = source as Dog;
	
	/* value, is this instance, will be `null` */
	var value = source as Plankton;

As casting basically does a check to see if the type is convertible and if so returns the value. If not it returns null.
	
###Are these the same?

In essense *no*. Using [ILSpy](http://ilspy.net/) we can see that the generated `IL` for `explicit casts` uses the [castclass](msdn.microsoft.com/library/system.reflection.emit.opcodes.castclass.aspx), where as the `as` operator uses [isinst](http://msdn.microsoft.com/library/system.reflection.emit.opcodes.isinst.aspx). 

From a little bit of testing it seems that 'as' is also faster. Others could confirm this but in tight loops `isinst` performs significantly faster than `castclass`.

![castclass vs. isinst](/images/castclass-vs-isinst.png)

Here's my quick-and-dirty test. 
<script src="https://gist.github.com/Buildstarted/e6646dfd8fcf000efd50.js"></script>
Feel free to fork it and make improvements. There might be instances where castclass is faster.

It's not perfect but enough to convince me that `isinst` is a bit faster in tight loops than `castclass`

-**Ben Dornis**