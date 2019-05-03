---
layout:     post
title:      Some Essential C# Questions
subtitle:   What I usually ask and I had been asked
date:       2019-05-02
author:     madranger
header-img: img/post-bg-code.jpg
catalog: true
tags:
    - Coding
typora-root-url: ..
---

Photo by [Markus Spiske](https://unsplash.com/photos/xekxE_VR0Ec?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)



> **1 - What’s the difference between Class and Struct? Any performance concern? When you are coding, how do you choose Class and Struct?**

- **Diff#1: Heap vs. Stack.** `Reference types` and `Value types` are crucial in making this choice. The first difference between reference types and value types we will consider is that reference types are allocated on the heap and garbage-collected, whereas value types are allocated either on the stack or inline in containing types and deallocated when the stack unwinds or when their containing type gets deallocated. Therefore, allocations and deallocations of value types are in general cheaper than allocations and deallocations of reference types.
- **Diff#2: Memory usage** The next difference is related to `Memory usage`. Value types get boxed when cast to a reference type or one of the interfaces they implement. They get unboxed when cast back to the value type. Because boxes are objects that are allocated on the heap and are garbage-collected, too much boxing and unboxing can have a negative impact on the heap, the garbage collector, and ultimately the performance of the application. In contrast, no such boxing occurs as reference types are cast.
- **Diff#3: Reference and changes** Finally, reference types are passed by reference, whereas value types are passed by value. Changes to an instance of a reference type affect all references pointing to the instance. Value type instances are copied when they are passed by value. When an instance of a value type is changed, it of course does not affect any of its copies. Because the copies are not created explicitly by the user but are implicitly created when arguments are passed or return values are returned, value types that can be changed can be confusing to many users. Therefore, value types should be immutable.
- **As a rule of thumb** The majority of types in a framework should be classes. There are, however, some situations in which the characteristics of a value type make it more appropriate to use structs
- **CONSIDER** Defining a struct instead of a class if instances of the type are `small` and `commonly short-lived` or are `commonly embedded` in other objects. Defining a struct unless the type has all of the following characteristics: 
	- It logically represents a single value, similar to primitive types (int, double, etc.). 
	- It has an instance size under 16 bytes. 
	- It is immutable. 
	- It will not have to be boxed frequently. 
	- In all other cases, you should define your types as classes.
	
	

> **2 - Difference between Abstract Class and Interface**

- **Abstract Class**  
	- An abstract class is a special kind of class that cannot be instantiated but it allows other classes to inherit. 
	- A contract to define hierarchies. 
	- Can define fields/properties and completed methods
	- If a function is marked as `abstract`，cannot implement this method. Only for sub class to inherit and implement.
	- Can use access modifiers such as `Pubic`, `Protect`, `default` 
	- Not support multiple inheritance
	- A sub class can only have 1 abstract class.
	- Define core identity of a class. All sub class need to obey.
	- Fast speed
	- Better for refactoring. If need to add a new method, you can add into abstract method and give a default implementation without changing sub classes. 

- **Interface**
	- It's not a class. There is no implementation of methods, just signature.
	- A contract to define hierarchies. Can only define methods, but not fields
	- A class can implement more than 1 interfaces.
	- Cannot use access modifiers. All methods are `Public` by default
	- Define peripheral ability of a class. E.g. human and vehicle can inherit from a IMovable interface.
	- Take more time to find actual method.
	- More effort for refactoring. If need to add a new method, need you define the contract in Interface then update all implementations in all classes.
	- Cannot have static fields or methods. Interface is a contract. To use the contract you'll need an instance of the type. Static members are not applied to an instance but shared across all instances. Therefore a static member on an interface doesn't make sense because you could never call it. 
	- You can use use a interface to inherit another. but in class, you need to implement both.

    		```
		interface IMovable
		{
		    void MoveForward();
		}
		```
		
		```
		interface ITalk : IMovable
		{
		    void Talk();
		}
		
    public class Human : ITalk
		{
		    public void Talk() { }
		
			// need to implement MoveForward() too
		}
		```
		
		


> 3 - Difference between `protected` and `virtual` in Abstract class**

- `protected` means that it is visible only inside this class and classes derived from it. it should be compared with `public`, `private`

- `virtual` means that it can be `overriden` in derived classes.

- You can define a method below. it means you can override it in sub-class but not only visible inside of this class or lower-level classes.  
  
		```
	interface IMovable
	{
	    void MoveForward();
	}
	```
	
	```
	public abstract class Object
	{
	    protected virtual void Read()
	    {
	        //...nothing
	    }
	}
	
	public class People : Object, IMovable
	{
	    public void MoveForward() { }
	
	    //you are not necessary to implement Read
	}
	```



> **4 - Difference between `abstract method` and `virtual method` in Abstract class**

- `Abstract method` must be inside of abstract class. cannot be implement in abstract, but must be implement in sub class.
- `virtual method` can be in other type of class. can be implement in class or override in sub class



> **5 - What's explicit interface? why?**

- `Implicit`: you access the interface methods and properties as if they were part of the class.

- `Explicit`: you can only access methods and properties when treating the class as the implemented interface.

		~~~
	interface IControl
	{
	    void Paint();
	}
	interface ISurface
	{
	    void Paint();
	}
	~~~
	
	```
	class SampleClass : IControl, ISurface
	{
	    // Both ISurface.Paint and IControl.Paint call this method. 
	    public void Paint()
	    {
	        Console.WriteLine("Paint method in SampleClass");
	    }
	}
	
	//have different implementation for each interface
	public class SampleClass_2 : IControl, ISurface
	{
	    void IControl.Paint()
	    {
	        System.Console.WriteLine("IControl.Paint");
	    }
	    void ISurface.Paint()
	    {
	        System.Console.WriteLine("ISurface.Paint");
	    }
	}
	```



> **6 - Different between ref and out keywords when you define an argument in a method.**

- `Ref`, is used to pass an argument as a reference. This means that when value of that parameter is changed in the method, it gets reflected in the calling method. An argument that is passed using a ref keyword **MUST be initialized in the calling method** before it is passed to the called method.
- `Out`, is also used to pass an argument like ref keyword, but the argument **should be passed without assigning any value** to it. An argument that is passed using an out keyword must be initialized in the called method before it returns back to calling method.
- Both ref and out can be used in same method.



> **7 - Different between `Override` and `Overload`**

- `Override`, for virtual method. implement it in sub-class
- `Overload`, there are more than one method with same name but different parameter.



> **8 - What's `Delegate` and `Event`**

- **`Delegate`**
	- Delegates can refer to a method, and it is quite similar to the function pointer in C++.  Delegates hold the reference of the methods 
	- Outside of any class
	- Delegates are independent of event.
	- The signature of the method reference by the delegate must match the delegate signature
	- It usually used then the method will be invoked by the delegate is determined at the runtime. 
	- You simply have to create a delegate object and assign the methods one after other to the chain using  ” += ” operator. You can also unchain a method using “-=” operator.
	- Syntax

			delegate <return type> delegate_name(parameter_list);	
		```
		//Example
		public delegate String StringDelegate(Int32 value);
		
		//normal way
		StringDelegate del = new StringDelegate(The_Method);
		//or
		StringDelegate del = The_Method;

		//anonymous delegate
		delegate_name del = (x) => { return x.ToString(); };
		```
	- When to use it?
		- To create anonymous method
		- To create event
		- It is desirable to encapsulate a static method.
		- A class may need more than one implementation of the method.
		- For example, you cannot access a Control 'textBox1' from a thread other than the thread it was created on. refer to [Delegate Sample](https://www.cnblogs.com/flyinthesky/archive/2009/12/15/1624568.html+&cd=1&hl=en&ct=clnk&gl=us)


- **`Event`**
	- Events notify that some action has been performed. Event provides a way to access that method using delegates.
	- Inside of a class
	- The event cannot be created without delegates.
	- The event handlers can be added to the chain using the operator “+=” and can be unchained using the operator “-=” Both static and instance method can be used as an event handler.
	- Syntax

			event event_delegate event_name;
			//[event_delegate] show the name of the delegate that is supporting the event.

		```
		//Example
public delegate void PrintEventHandler(String msg);
		
		class TestRoom
		{
		    public event PrintEventHandler OnPrint;
		
		    public void Action(String  msg)
		    {
		        if (OnPrint != null)
		            OnPrint(msg);
		        else
		        {
		            Console.WriteLine("No event registered, print by default function: {0}", msg.Trim());
		        }
		    }
}
		
		//To use it
		class Program
		{
		    static void CustomPrint(String msg)
		    {
		        Console.WriteLine("Customized function! --> {0}", msg);
		    }
		    static void Main(string[] args)
		    {
		        TestRoom tr = new TestRoom();
		        tr.Action("Hello");
		        tr.OnPrint += CustomPrint;
		        tr.Action("Hello Again");
		        tr.OnPrint -= CustomPrint;
		        tr.Action("Bye");
		    }
		}
		```
		
	
	

> **9 - When to use Delegates instead of Interfaces?**

- Both delegates and interfaces enable a class designer to separate type declarations and implementation. 
- A given interface can be inherited and implemented by any class or struct. 
- A delegate can be created for a method on any class, as long as the method fits the method signature for the delegate. 
- An interface reference or a delegate can be used by an object that has no knowledge of the class that implements the interface or delegate method. 



> **10 - What is Extension Method? How to design it?**

- Extension methods enable you to "add" methods to existing types without creating a new derived type, recompiling, or otherwise modifying the original type. 

- Extension methods are a special kind of **static** method, but they are called as if they were instance methods on the extended type.

- To define it, create a static class and define a static method with `this` keyword of parameter.

		~~~
	public static class StringExt
	{
	    public static String FullTrim(this String value)
	    {
	        return value.Trim();
	    }
	}
	~~~



> **11 - What is LinQ? What is Lambda Exception? Difference?**

- **`LinQ`** stands for Language Integrated Query. A query is an expression that retrieves data from a data source. 

- LINQ comes in two flavours – using **`Lambda Expressions`** and using **`SQL-like Query  Expressions`**

		~~~
	Func<int, bool> isEven = i => i % 2 == 0;
		int[] ints = new int[] { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
	~~~
	
	```
	// using Query expression
	var evensQuery = from i in ints where isEven(i) select i;
	// using Lambda expression
	var evensLambda = ints.Where(isEven);
	```
	
- LINQ queries are based on generic types

   ~~~
   IEnumerable<Customer> customerQuery =
   						    from cust in customers
   						    where cust.City == "London"
   						    select cust;
   ~~~

   ```
   foreach (Customer customer in customerQuery)
   {
       Console.WriteLine(customer.LastName + ", " + customer.FirstName);
   }
   ```

- **`Lambda expression`** is actually a kind of anonymous method and more of a like delegate type.



> **12 - What is Generic method and class?**

- What? Why? How does it work in CLR?
	- To encapsulate operations those are not specific to a particular data type.
	- [How does it work in CLR](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generics-in-the-run-time)
	- Generally, for value type. Take List<T> as example, CLR will create unique List<T> instance for int and long type. But for reference type, probably no different of object reference size, so CLR will reuse the List<T> but fill other reference type


- **Advantage**
	- Generics provide type safety without the overhead of multiple implementations.
	- Generics eliminate boxing and unboxing.
	- There is no need to write code to test for the correct data type because it is enforced at compile time. The need for type casting and the possibility of run-time errors are reduced.
	- By providing strong typing, a class built from a generic lets visual studio provide IntelliSense.
	- Generic collection types generally perform better for storing and manipulating value types because there is no need to box the value types
	- Generic delegates enable type-safe callbacks without the need to create multiple delegate classes.


- **Disadvantage**
	- Generic types can be derived from most base classes, such as MarshalByRefObject (and constraints can be used to require that generic type parameters derive from base classes like MarshalByRefObject). However, the .NET Framework does not support context-bound generic types. A generic type can be derived from ContextBoundObject, but trying to create an instance of that type causes a TypeLoadException.
	- Enumerations cannot have generic type parameters. **NOTE:** C# 7.3 is support!
	- Lightweight dynamic methods cannot be generic.
	- In C#, a nested type that is enclosed in a generic type cannot be instantiated unless types have been assigned to the type parameters of all enclosing types


- **Any performance concern?**
	
	- It depends on how you use it. If process value type. May not a good choice. But for strong reference type, there's a significant performance benefit to using generics -- you do away with boxing and unboxing
	
	


> **13 - Performance between `List<T>` and `ArrayList`?**

- This is mainly about boxing and unboxing
- **List<T>** to store value of a specific type without boxing and unboxing of object. But ArrayList doesn't.
- From thread safety view:
	- **ArrayList** provides some thread-safety through the Synchronized property, which returns a thread-safe wrapper around the collection. The wrapper works by locking the entire collection on every add or remove operation. Therefore, each thread that is attempting to access the collection must wait for its turn to take the one lock. This is not scalable and can cause significant performance degradation for large collections
	- **List<T>** does not provide any thread synchronization; user code must provide all synchronization when items are added or removed on multiple threads concurrently
	
	

> **14 - What's the best practice to manage/release C#.NET object and resources?**

- Distinguish managed code and unmanaged code first. If managed code, usually GC will collect automatically. If unmanaged code, need to create explicit disposal function to inherit IDispose interface. Depends on what kind of object using in program. E.g. image type, COM object.
- For DB connection, close any connection in finally block to avoid risk while exception is occurring.
- Be careful of object type casting, especially String



> **15 - What’s Managed and Unmanaged objects?**

- **Managed objects** are created, managed and under scope of CLR, pure .NET code managed by runtime, Anything that lies within .NET scope and under .NET framework classes such as string, int, bool variables are referred to as managed code.
- **Unmanaged objects** are created outside the control of .NET libraries and are not managed by CLR, example of such unmanaged code is COM objects, file streams, connection objects, Interop objects. (Basically, third party libraries that are referred in .NET code.)



> **16 - What’s Garbage Collection?**

- When you create any object in C#, CLR allocates memory for the object from heap. This process is repeated for each newly created object, but there is a limitation to everything, Memory is not un-limited and we need to clean some used space in order to make room for new objects. Hence, the concept of garbage collection is introduced, Garbage collector manages allocation and reclaiming of memory. GC makes a trip to the heap and collects all objects that are no longer used by the application and then makes them free from memory.
- <u>Virtual memory has three blocks:</u>
	- Free (empty space)
	- Reserved (already allocated)
	- Committed (This block is give-out to physical memory and not available for space allocation)
- Basicly, Heap is managed by different 'Generations', it stores and handles long-lived and short-lived objects, see the below generations of Heap:
	- **0 Generation (Zero)**: This generation holds short-lived objects, e.g., Temporary objects. GC initiates garbage collection process frequently in this generation.
	- **1 Generation (One)**: This generation is the buffer between short-lived and long-lived objects.
	- **2 Generation (Two)**: This generation holds long-lived objects like a static and global variable, that needs to be persisted for a certain amount of time. Objects which are not collected in generation Zero, are then moved to generation 1, such objects are known as survivors, similarly objects which are not collected in generation One, are then moved to generation 2 and from there onwards objects remain in the same generation.
- <u>How GC Decides If Objects Are Live?</u>
	- It collects all handles of an object that are allocated by user code or by CLR
	- Keeps track of static objects, as they are referenced to some other objects
	- Use stack provided by stack walker and JIT
- <u>When will GC will be triggered?</u>
There are no specific timings for GC to get triggered, GC automatically starts operation on the following conditions:
	- When virtual memory is running out of space.
	- When allocated memory is suppressed acceptable threshold (when GC found if the survival rate (living objects) is high, then it increases the threshold allocation).
	- When we call `GC.Collect()` method explicitly, as GC runs continuously, we actually do not need to call this method.
- <u>How to clean objects?</u>
GC is responsible to track the life time of all managed and unmanaged objects but still GC is not aware of releasing unmanaged resources. There are different ways to cleanup unmanaged resources:
	- Implement IDisposable interface and Dispose method
	- 'using' block is also used to clean unmanaged resources
	
	

> **17 - String**

- String is a `Reference Type`
- <u>What’s the difference with other normal reference type?</u>
	- **[String pool](https://docs.microsoft.com/en-us/dotnet/api/system.string.intern?redirectedfrom=MSDN&view=netframework-4.8#System_String_Intern_System_String_)**. The string intern pool is a **table** that contains a **single reference** to each unique literal string declared or created programmatically in application domain. The CLR uses the intern pool to minimize string storage requirements. As a result, an instance of a literal string with a particular value only exists once in the system


- <u>When you use String type, what would you consider? (best practice)</u>
	- Resource management. 
	- How to concatenate string. 
	- Be careful of using ToString function.
	- Comparison


- <u>Why does StringBuilder has better performance? Will it always be high performance when concatenating (or linking) multiple strings?</u>
	- `StringBuilder` class is that it is mutable and String class on the other hand is immutable
	- The StringBuilder internally **uses an array of characters to work on any changes to the string** like concatenation. As soon as we instantiate StringBuilder class, an array of the characters is created with size of 16 characters which is known as the capacity of the StringBuilder class. Arrays are better to work from the performance point of view as these are continuous blocks of memory created, which makes foster to retrieve or insert data into an array.
	
	


> **18 - What is Partial class and what all are the advantages of Partial class?**

- The class declaration can be partitioned into several partial class declarations.
	- Each of the partial class declarations contains the declarations of some of the class members.
	- The partial class declarations of a class can be in the same file or in different files.
	- Each partial declaration must be labeled as partial class, in contrast to the single keyword class.
- Advantage
	
	- Multiple developers can work on the same class easily.
	
	

> **19 - What is Late Binding (Concept Of Polymorphism)?**

- `Polymorphism` plays an important role in allowing the different objects to share the same external interface although the implementation may be different.
- Binding is the process of locating the declaration that corresponds to a uniquely specified type. When this process occurs at run time rather than at compile time.
- Late binding is achieved, using virtual methods, the virtual methods are overridden in the derived class as per the specific requirement.



> **20 - What is Custom Binding?**

- The CLR supports multiple programming languages, and the binding rules of these languages differ. In the early-bound case, code generators can completely control this binding. However, in late binding through reflection, binding must be controlled by customized binding. The Binder class provides custom control of member selection and invocation.
- Using custom binding, you can load an assembly at run time, obtain information about types in that assembly, specify the type that you want, and then invoke methods or access fields or properties on that type. This technique is useful if you do not know an object's type at compile time, such as when the object type is dependent on user input.



> **21 - Reflection**

- <u>What is Reflection?</u>
	- Reflection provides objects (of type Type) that describe assemblies, modules and types. You can use reflection to **dynamically** create an instance of a type, bind the type to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties.
	- Useful in following circumstances
		- When you have to access attributes in your program's metadata. For more information, see Retrieving Information Stored in Attributes. 
		- For examining and instantiating types in an assembly. 
		- For building new types at runtime. Use classes in System.Reflection.Emit. 
		- For performing late binding, accessing methods on types created at run time. See the topic Dynamically Loading and Using Types


- <u>How does reflection works?</u>
	- A CLR assembly (executable or dynamic link library) typically contains metadata about its structure, which means information about the types, the structures, the methods, the fields, their names and a bunch of other information.
	- Reflection assembly is a powerful tool to allow you to dynamically retrieve information from metadata on types at runtime. Something similar with RTTI (runtime type information) in C++.


- <u>When do you need to use reflection?</u>
	- When you have to access attributes in your program's metadata. For more information, see Retrieving Information Stored in Attributes. 
	- For examining and instantiating types in an assembly. 
	- For building new types at runtime. Use classes in `System.Reflection.Emit`. 
	- For performing late binding, accessing methods on types created at run time. See the topic Dynamically Loading and Using Types. 
- <u>Ppl always say the performance of reflection is poor? What's your thoughts?</u>
  - When you have to access attributes in your program's metadata. For more information, see Retrieving Information Stored in Attributes. 
  - Generally it’s slower than non-reflection code.
  - It was never designed to have high-performance as one of its goals. But helps you to access information that you cannot determine until the code is running.
  - Read this <http://mattwarren.org/2016/12/14/Why-is-Reflection-slow>
  
  

> **22 - Exception**

- <u>What is Exception?</u>
  -  a problem that arises during the execution of a program
- <u>Can you use `try` without `catch`?</u>
  - Yes, but you must specify a `finally` block.
- <u>In Catch(ex) clause, what’s the difference between `throw` and `throw ex`?</u>
  - `throw` will throw the original exception stack track info to upper level directly.
  - `throw ex` will override the original `ex` and return to upper level. You will lose original exception info.