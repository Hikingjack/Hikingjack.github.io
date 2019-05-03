---
layout:     post
title:      3 Design Patterns You May Keep In Mind
subtitle:   What I usually ask and I had been asked
date:       2019-05-03
author:     madranger
header-img: img/post-bg-code.jpg
catalog: true
tags:
    - Coding
typora-root-url: ..
---

Photo by [Markus Spiske](https://unsplash.com/photos/xekxE_VR0Ec?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)



## Dependency Injection

#### Good to know

- **Inversion of Control Principle (IoC)** - Normally, the flow of the program logic is determined by objects that are bound to one another. With the inversion of control, the flow depends on the defined abstractions to be implemented that is built up **during program execution**. This principle can be achieved by techniques like dependency injection. In IoC, the code could also be linked statically during compilation to the defined interface functions, but finding the implementation of the function to execute by reading its description from external configuration instead of with a direct reference in the code itself.
- **Dependency Injection (DI)** - is an object-oriented programming design pattern that allows us to develop loosely coupled code. It helps in getting rid of tightly coupled software components. The purpose is to make code maintainable and easy to update.

#### Traditional Programming

All objects are known and used in code before compiling. Take payment as an example, you have to change lots of source code if you change from Apple Pay to Google Pay.

~~~
public class ShoppingLogic
{
    private OrderService _svcOrder;
    private PaymentService _svcPay;
    private DeliveryService _svcDeliver;

    public ShoppingLogic()
    {
        _svcOrder = new OrderService();
        _svcPay = new ApplePaymentService();
        _svcDeliver = new DeliveryService();
    }

    public void Purchase(double price, string contact, string address)
    {
        var order = _svcOrder.PlaceOrder(price, contact, address);
        var paymentResult = _svcPay.Transfer(order.number, order.price);
        if(paymentResult.Result)
        	_svcDeliver.Send(order.number, contact, address);
        //...other logics
    }
}

//Services ------------------
public class OrderService
{
    public Order PlaceOrder(double price, string contact, string address)
    {
        return new Order(price, contact, address);
    }
}

public class ApplePaymentService // The service we are going to change
{
    public PaymentResult Transfer(string order_number, double price)
    {
        return new PaymentResult(/*other param*/) 
        			{ Result = true; }; // mimic successful payment
    }
}

public class DeliveryService
{
    public void Send(string order_number, string contact, string address)
    {
        // Deliver...
    }
}

//Business objects -----------------
public class Order
{
    private double _price;
    private string _contact;
    private string _address;
    public Order(double price, string contact, string address)
    {
        _price = price;
        _contact = contact;
        _address = address;
    }
}

//... other objects or components
~~~



In order to ease the effort to switch different payment service, we may define an interface to `decouple` the business logic and services.

~~~
public interface IPaymentService
{
	void Transfer(string order_number, double price);
}

public class ApplePaymentService : IPaymentService // Updated
{
    public PaymentResult Transfer(string order_number, double price)
    {
        return new PaymentResult() { Result = true; }; // mimic successful payment
    }
}

public class GooglePaymentService : IPaymentService // New Added
{
    public PaymentResult Transfer(string order_number, double price)
    {
        return new PaymentResult() { Result = true; }; // mimic successful payment
    }
}
~~~



The `ShoppingLogic` can be updated as below

~~~
public class ShoppingLogic
{
    private OrderService _svcOrder;
    private IPaymentService _svcPay; // updated#1
    private DeliveryService _svcDeliver;

    public ShoppingLogic()
    {
        _svcOrder = new OrderService();
        _svcPay = new ApplePaymentService(); // can be change to any other payment type
        _svcDeliver = new DeliveryService();
    }

    public void Purchase(double price, string contact, string address)
    {
        var order = _svcOrder.PlaceOrder(price, contact, address);
        var paymentResult = _svcPay.Transfer(order.number, order.price);
        if(paymentResult.Result)
        	_svcDeliver.Send(order.number, contact, address);
        //...other logics
    }
}
~~~



However, obviously the core logic is still tightly coupled. We may start thinking about the best way to tidy it up.

#### Constructor Injection

~~~
public class ShoppingLogic
{
    private OrderService _svcOrder;
    private IPaymentService _svcPay; // updated#1
    private DeliveryService _svcDeliver;

    public ShoppingLogic(IPaymentService svcPay)
    {
        _svcOrder = new OrderService();
        _svcPay = svcPay; // updated#2
        _svcDeliver = new DeliveryService();
    }

    public void Purchase(double price, string contact, string address)
    {
        var order = _svcOrder.PlaceOrder(price, contact, address);
        var paymentResult = _svcPay.Transfer(order.number, order.price);
        if(paymentResult.Result)
        	_svcDeliver.Send(order.number, contact, address);
        //...other logics
    }
}

//Use ShoppingLogic
class Program
{
    static void Main(string[] args)
    {
        IPaymentService svcPay = new ApplePaymentService();
        ShoppingLogic logic = new ShoppingLogic(svcPay);
    }
}
~~~



#### Setter Injection

~~~
public class ShoppingLogic
{
    private OrderService _svcOrder;
    private IPaymentService _svcPay; // updated#1
    private DeliveryService _svcDeliver;
    
    // updated#3
    public IPaymentService PayService
    {
        get { return _svcPay; }
        set { _svcPay = value; }
    }

    public ShoppingLogic()
    {
        _svcOrder = new OrderService();
        //_svcPay = svcPay; // removed
        _svcDeliver = new DeliveryService();
    }

    //...other logics
}

//Use ShoppingLogic
class Program
{
    static void Main(string[] args)
    {
        IPaymentService svcPay = new ApplePaymentService();
        ShoppingLogic logic = new ShoppingLogic() { PayService = svcPay; };
    }
}
~~~



#### Method Injection

To specify the service as a parameter.

~~~
public class ShoppingLogic
{
    private OrderService _svcOrder;
    //private IPaymentService _svcPay; // removed
    private DeliveryService _svcDeliver;
    
    public ShoppingLogic()
    {
        _svcOrder = new OrderService();
        //_svcPay = svcPay; // removed
        _svcDeliver = new DeliveryService();
    }

    public void Purchase(double price, string contact, string address, IPaymentService svcPay) // updated #4
    {
        var order = _svcOrder.PlaceOrder(price, contact, address);
        var paymentResult = _svcPay.Transfer(order.number, order.price);
        if(paymentResult.Result)
        	_svcDeliver.Send(order.number, contact, address);
        //...other logics
    }
}
~~~





## Singleton Pattern

#### Good to know

A singleton is a class which only allows one instance of itself to be created - and gives simple, easy access to said instance. Usually we will define logging class with singleton pattern.

Most commonly, singletons don't allow any parameters to be specified when creating the instance.

**Four common characteristics**:

- A single constructor, which is private and parameterless. This prevents other classes from instantiating it (which would be a violation of the pattern). Note that it also prevents sub-classing - if a singleton can be sub-classed once, it can be sub-classed twice, and if each of those subclasses can create an instance, the pattern is violated. The factory pattern can be used if you need a single instance of a base type, but
  the exact type isn't known until runtime.
- The class is **sealed**. This is unnecessary, strictly speaking, due to the above point, but may help the JIT to optimize things more. 
- A **static variable** which holds a reference to the single created instance, if any.
- A **public static** means of getting the reference to the single created instance, creating one if necessary.



#### Sample 1 - Not Thread-safe

Bad code! Don't use this version.

~~~
public sealed class Singleton1
{
    private static Singleton1 instance = null;

    private Singleton1() { }

    public static Singleton1 Instance
    {
        get
        {
            if (instance == null)
            {
                instance = new Singleton1();
            }
            return instance;
        }
    }
}
~~~



#### Sample 2 - Simple Thread-safe

~~~
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();

    Singleton() { }

    public static Singleton Instance
    {
        get
        {
            lock (padlock) // Lock an object
            {
                if (instance == null)
                {
                    instance = new Singleton();
                }
                return instance;
            }
        }
    }
}
~~~



#### Sample 3 - Attempted thread-safety

using double-check locking. Bad code! Don't use this version.

~~~
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();

    Singleton() { }

    public static Singleton Instance
    {
        get
        {
            if (instance == null)
            {
                lock(padlock)
                {
                    if(instance ==null)
                    {
                        instance = new Singleton();
                    }
                }
            }
            return instance;
        }
    }
}
~~~

This implementation attempts to be thread-safe without the necessity of taking out a lock every time. 

Unfortunately, there are four downsides to the pattern:

- **It doesn't work in Java**. This may seem an odd thing to comment on, but it's worth knowing if you ever need the singleton pattern in Java, and C# programmers may well also be Java programmers. The Java memory model doesn't ensure that the constructor completes before the reference to the new object is assigned to instance. The Java memory model underwent a reworking for version 1.5, but double-check locking is still broken after this without a volatile variable (as in C#).
- Without any memory barriers, it's broken in the ECMA CLI specification too. It's possible that under the .NET 2.0 memory model (which is stronger than the ECMA spec) it's safe, but I'd rather not rely on those stronger semantics, especially if there's any doubt as to the safety. Making the instance variable volatile can make it work, as would explicit memory barrier calls, although in the latter case even experts can't agree exactly which barriers are required. I tend to try to avoid situations where experts don't agree what's right and what's wrong!
- It's easy to get wrong. The pattern needs to be pretty much exactly as above - any significant changes are likely to impact either performance or correctness.
- It still doesn't perform as well as the later implementations.



#### Sample 4 - Not quite as lazy

But thread-safe without using locks.

```
public sealed class Singleton
{
    private static readonly Singleton instance = new Singleton();

    // Explicit static constructore to tell C# compiler
    // not to mark type as beforefieldinit
    static Singleton() { }

    private Singleton() { }

    public static Singleton Instance
    {
        get { return instance; }
    }
}
```

As you can see, this is really is extremely simple - but why is it thread-safe and how lazy is it? 

Well, static constructors in C# are specified to execute only when an instance of the class is created or a static member is referenced, and to execute only once per AppDomain. Given that this check for the type being newly constructed needs to be executed whatever else happens, it will be faster than adding extra checking as in the previous examples. 

There are a couple of wrinkles, however:

- It's not as lazy as the other implementations. In particular, if you have static members other than Instance, the first reference to those members will involve creating the instance. This is corrected in the next implementation.

- There are complications if one static constructor invokes another which invokes the first again. Look in the .NET specifications (currently section 9.5.3 of partition II) for more details about the exact nature of type initializers - they're unlikely to bite you, but it's worth being aware of the consequences of static constructors which refer to each other in a cycle.
- The laziness of type initializers is only guaranteed by .NET when the type isn't marked with a special flag called `beforefieldinit`. Unfortunately, the C# compiler (as provided in the .NET 1.1 runtime, at least) marks all types which don't have a static constructor (i.e. a block which looks like a constructor but is marked static) as `beforefieldinit`. I now have an article with more details about this issue. Also note that it affects performance, as discussed near the bottom of the page.

One shortcut you can take with this implementation (and only this one) is to just make instance a public static readonly variable, and get rid of the property entirely. This makes the basic skeleton code absolutely tiny! Many people, however, prefer to have a property in case further action is needed in future, and JIT inlining is likely to make the performance identical. (Note that the static constructor itself is still required if you require laziness.)



#### Sample 5 - Full Lazy Instantiation

~~~
public sealed class Singleton
{
    private Singleton() { }

    private class Nested
    {
        // Explicit static constructor to tell C# compiler
        // not to mark type as beforefieldinit
        static Nested() { }
        internal static readonly Singleton instance = new Singleton();
    }

    public static Singleton Instance { get { { return Nested.instance; } } }
}
~~~

Here, instantiation is triggered by the first reference to the static member of the nested class, which only occurs in Instance. This means the implementation is fully lazy, but **has all the performance benefits of the previous ones**. Note that although nested classes have access to the enclosing class's private members, the reverse is not true, hence the need for instance to be internal here. That doesn't raise any other problems, though, as the class itself is private. The code is a bit more complicated in order to make the instantiation lazy, however.



#### Sample 6 - Using Lazy<T> in .NET 4

~~~
public sealed class Singleton
{
    private static readonly Lazy<Singleton> lazy = new Lazy<Singleton>(() => new Singleton());

    public static Singleton Instance { get { return lazy.Value; } }

    private Singleton() { }
}
~~~

- If you're using .NET 4 (or higher), you can use the `System.Lazy<T>` type to make the laziness really simple. All you need to do is pass a delegate to the constructor which calls the Singleton constructor - which is done most easily with a lambda expression.
- It's simple and performs well. It also allows you to check whether or not the instance has been created yet with the `IsValueCreated` property, if you need that.
- The code above implicitly uses `LazyThreadSafetyMode.ExecutionAndPublication` as the thread safety mode for the `Lazy<Singleton>`. Depending on your requirements, you may wish to experiment with other modes.



## Strategic Pattern

#### Good to know

Strategy pattern defines a family of algorithms, encapsulates each one of them and makes them interchangeable.

- **Family of Algorithms** - The definition says that the pattern defines the family of algorithms- it means we have functionality (in these algorithms) which will do the same common thing for our object, but in different ways.
- **Encapsulate each one of them** - The pattern would force you to place your algorithms in different classes (encapsulate them). Doing so would help us in selecting the appropriate algorithm for our object.
- **Make them interchangeable** - The beauty with strategy pattern is we can select at run time which algorithm we should apply to our object and can replace them with one another.

![](/img/coding/strategic_pattern_01.png)



![](/img/coding/strategic_pattern_02.png)

#### Sample

- Define strategies.

~~~
//Enum fir different types of sortings
public enum ObjectToSort
{
    StudentNumber,
    RailwayPassengers,
    CountyResidents
}

//Interface for the sorting strategy
public interface ISortingStrategy
{
    void Sort<T>(List<T> dataToBeSorted);
}

//Alogrithm-Quicksort
public class QuickSort : ISortingStrategy
{
    public void Sort<T>(List<T> dataToBeSorted)
    {
        //Logic for quick sort
    }
}

//Algorithm-MergeSort
public class MergeSort : ISortingStrategy
{
    public void Sort<T>(List<T> dataToBeSorted)
    {
        //Logic for merge sort
    }
}

//Algorithm-HeapSort
public class HeapSort : ISortingStrategy
{
    public void Sort<T>(List<T> dataToBeSorted)
    {
        //Logic for heap sort
    }
}
~~~

- How to use them

```
class Program
{
    static void Main(string[] args)
    {
        ISortingStrategy sortingStrategy = null;
        //Sort the CountyResidents
        List<String> countyRes = new List<string> { "AD", "AC", "AX", "ZW" };
        sortingStrategy = GetSortingOption(ObjectToSort.CountyResidents);
        sortingStrategy.Sort(countyRes);
    }

    private static ISortingStrategy GetSortingOption(ObjectToSort objToSort)
    {
        ISortingStrategy sortingStrategy = null;

        switch (objToSort)
        {
            case ObjectToSort.StudentNumber:
                sortingStrategy = new MergeSort(); break;
            case ObjectToSort.RailwayPassengers:
                sortingStrategy = new HeapSort(); break;
            case ObjectToSort.CountyResidents:
                sortingStrategy = new QuickSort(); break;
            default:
                break;
        }
        return sortingStrategy;
    }
}
```





## Factory Pattern

#### Good to know

- Defined an interface or abstract class to allow it’s sub-class to determine what kind of object need to be instantiate. The instantiate process is involved in sub-class.
- Create different type in different circumstance
- Needs to specify the class name while creating the objects

![](/img/coding/factory_pattern_01.png)

- Components

  - **Product** - defines the interface of objects the factory method creates.

  - **ConcreteProduct** - a class which implements the Product interface.
  - **Creator** - an abstract class and declares the factory method, which returns an object of type Product. This may also define a default implementation of the factory method that returns a default `ConcreteProduct` object. This may call the factory method to create a Product object.
  - **ConcreteCreator** - a class which implements the Creator class and overrides the factory method to return an instance of a `ConcreteProduct`



#### Simple (static) Factory

**Disadvantage** - Not flexible. Need to hardcode the type. So need to update the class once new type comes in.



> Sample

- Product

~~~
/// <summary>
/// The 'Product' Abstract Class
/// </summary>
abstract class CellPhone
{
    public abstract String Brand { get; }
    public abstract String Model { get; set; }
    public abstract Double Price { get; set; }
}
~~~



- ConcreteProduct

~~~
/// <summary>
/// The first 'ConcreteProdct' class
/// </summary>
class iPhone : CellPhone
{
    private readonly string _brand;
    private String _model;
    private double _price;

    public iPhone(string model, double price)
    {
        _brand = "Apple";
        _model = model;
        _price = price;
    }

    public override string Brand
    {
        get { return _brand; }
    }

    public override string Model
    {
        get { return _model; }
        set { _model = value; }
    }

    public override double Price
    {
        get { return _price; }
        set { _price = value; }
    }
}

/// <summary>
/// The second 'ConcreteProdct' class
/// </summary>
class Huawei : CellPhone
{
    private readonly string _brand;
    private string _model;
    private double _price;

    public Huawei(string model, double price)
    {
        _brand = "Huawei";
        _model = model;
        _price = price;
    }

    // rest are the same as iPhone
}
~~~



- Creator

~~~
/// <summary>
/// The 'Creator' Abstract Class
/// </summary>
abstract class CellPhoneFactory
{
    public abstract CellPhone ProduceCellPhone();
}
~~~



- ConcreteCreator

```
/// <summary>
/// A 'ConcreteCreator' class
/// </summary>
class IPhoneFactory : CellPhoneFactory
{
    private string _model;
    private double _price;

    public IPhoneFactory(string model, double price)
    {
        _model = model;
        _price = price;
    }

    public override CellPhone ProduceCellPhone()
    {
        return new iPhone(_model, _price);
    }
}

/// <summary>
/// A 'ConcreteCreator' class
/// </summary>
class HuaweiFactory : CellPhoneFactory
{
    private string _model;
    private double _price;

    public HuaweiFactory(string model, double price)
    {
        _model = model;
        _price = price;
    }

    public override CellPhone ProduceCellPhone()
    {
        return new Huawei(_model, _price);
    }
}
```



- How to use it

~~~
class Program
{
    static void Main(string[] args)
    {
        CellPhoneFactory factory = null;
        Console.WriteLine("Enter the phone type you would like to buy: ");
        String phone = Console.ReadLine();

        switch(phone.ToLower())
        {
            case "iphone":
                factory = new IPhoneFactory("iPhone X", 6000);
                break;
            case "huawei":
                factory = new HuaweiFactory("P30 Pro", 6500);
                break;
            default: break;
        }

        if (factory != null)
        {
            var result = factory.ProduceCellPhone();
        }
        //...other logics
    }
}
~~~





#### Abstract Factory

Define an interface which will create families of related or dependent objects. In simple words, interface will expose multiple methods each of which will create some object. Again, here method return types will be generic interfaces. All these objects will together become part of some important functionality.

- Creates object through composition
- Produce families of products
- Concrete factories implements factory method to create product

![](/img/coding/factory_pattern_02.png)



The classes and objects participating in the above UML class diagram are as follow.

- **AbstractFactory** - an interface for operations which is used to create abstract product.
- **ConcreteFactory** - a class which implements the AbstractFactory interface operations to create concrete products.
- **AbstractProduct** - an interface for a type of product object.
- **Product** - a product object to be created by the corresponding concrete factory also implements the AbstractProduct interface.
- **Client** - a class which uses AbstractFactory and AbstractProduct interfaces to create a family of related objects.



>  Sample

![](/img/coding/factory_pattern_03.png)

The classes and objects participating in the above class diagram can be identified as shown below.

- **AbstractProduct** - ISmartPhone, INormalPhone

~~~
interface ISmartPhone
{
    String GetModelDetails();
}

interface INormalPhone
{
    String GetModelDetails();
}
~~~



- **AbstractFactory** - IMobilePhone

~~~
interface IMobilePhone
{
    ISmartPhone GetSmartPhone();
    INormalPhone GetNormalPhone();
}
~~~



- **ConcreteFactory** - Nokia, Samsung

~~~
class Nokia : IMobilePhone
{
    public INormalPhone GetNormalPhone()
    {
        return new Nokia7110();
    }

    public ISmartPhone GetSmartPhone()
    {
        return new NokiaN82();
    }
}

class Samsung : IMobilePhone
{
    public INormalPhone GetNormalPhone()
    {
        return new SamsungJet();
    }

    public ISmartPhone GetSmartPhone()
    {
        return new SamsungS10();
    }
}
~~~



- **Product** - Nokia7110, NokiaN82, SamsungJet,SamsungS10

~~~
class Nokia7110 : INormalPhone
{
    public string GetModelDetails()
    {
        return "Nokia7110";
    }
}

class SamsungJet : INormalPhone
{
    public string GetModelDetails()
    {
        return "SamsungJet";
    }
}

class NokiaN82 : ISmartPhone
{
    public string GetModelDetails()
    {
        return "NokiaN82";
    }
}

class SamsungS10 : ISmartPhone
{
    public string GetModelDetails()
    {
        return "SamsungS10";
    }
}
~~~



- **Client** - MobileClient

~~~
class MobileClient
{
    ISmartPhone smartPhone;
    INormalPhone normalPhone;

    public MobileClient(IMobilePhone factory)
    {
        smartPhone = factory.GetSmartPhone();
        normalPhone = factory.GetNormalPhone();
    }

    public string GetSmartPhoneModelDetails()
    {
        return smartPhone.GetModelDetails();
    }
    public string GetNormalPhoneModelDetails()
    {
        return normalPhone.GetModelDetails();
    }
}
~~~



- How to use it

~~~
class Program
{
    static void Main(string[] args)
    {
        IMobilePhone nokiaFactory = new Nokia();
        MobileClient nokiaClient = new MobileClient(nokiaFactory);

        //NOKIA
        var result1 = nokiaClient.GetNormalPhoneModelDetails();
        var result2 = nokiaClient.GetSmartPhoneModelDetails();
    }
}
~~~



> Summary

- **Applicability & Examples** - We should use the Abstract Factory design pattern when:
  - the system needs to be independent from the way the products it works with are created.
  - the system is or should be configured to work with multiple families of products.
  - a family of products is designed to work only all together.
  - the creation of a library of products is needed, for which is relevant only the interface, not the implementation, too.
- **Advantages**
  - **Isolation of concrete classes** - The Abstract Factory pattern helps you control the classes of objects that an application creates. Because a factory encapsulates the responsibility and the process of creating product objects, it isolates clients from implementation classes. Clients manipulate instances through their abstract interfaces. Product class names are isolated in the implementation of the concrete factory; they do not appear in client code.
  - **Exchanging Product Families easily** - The class of a concrete factory appears only once in an application, that is where it’s instantiated. This makes it easy to change the concrete factory an application uses. It can use various product configurations simply by changing the concrete factory. Because an abstract factory creates a complete family of products, the whole product family changes at once.
  - **Promoting consistency among products** - When product objects in a family are designed to work together, it’s important that an application use objects from only one family at a time. AbstractFactory makes this easy to enforce.n.
- **Disadvantages**
  - **Difficult to support new kind of products** - Extending abstract factories to produce new kinds of Products isn’t easy. That’s because the AbstractFactory interface fixes the set of products that can be created. Supporting new kinds of products requires extending the factory interface, which involves changing the AbstractFactory class and all of its subclasses.



#### Reflection Factory

Reflection factory allows you to implement Factory Pattern in a more concise way (less code).

- **Advantages**

  - The code is more flexible and configurable. save extra cost associated with instantiation of all derive objects as well as extra lines of code. Especially `Client class`.
  - Generic enough to be reused across the your code. Increases flexibility for adding or removing features.

- **Disadvantages**

  - Reflection reduces performance

  

> Sample

- **AbstractProduct** - ISmartPhone, INormalPhone `NO CHANGE`

- **AbstractFactory** - IMobilePhone `NO CHANGE`

- **ConcreteFactory** - Nokia, Samsung `NO CHANGE`

- **Product** - Nokia7110, NokiaN82, SamsungJet,SamsungS10 `NO CHANGE`

- **Client** - MobileClient `NEW APPROACH`

```
Using System.Reflection;

public static class MobileClient
{
    public static IMobileFactory GetFactory(string factoryName)
    {
        Assembly currAssembly = Assembly.GetExecutingAssembly();
        // May define customize enum & attribute for your products to refine this line.
        var currType = 
            currAssembly.GetTypes().SingleOrDefault(t => t.Name == factoryName);
        
        return (IMobileFactory)Activator.CreateInstance(currType);
    }
}
```



- How to use it

```
class Program
{
    static void Main(string[] args)
    {
        var nokiaFactory = MobileClient.GetFactory(typeof(Nokia).Name);
        INormalPhone phone = nokiaFactory.ProduceNormalPhone();
        Console.WriteLine("Got the phone: {0}", phone.GetModelDetails());

        Console.Read();
    }
}

output:
>> Got the phone: Nokia7110
```