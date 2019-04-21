
# SOLID Design Principle

The [pdf](assets\principles_and_patterns.pdf) named "Design Principles and Design Patterns" by Rober C. Martin is the best introduction to the concept. It does not have a Single Respoinsiblity in it.

+ **S**ingle Responsibility 
+ **O**pen Close principle
+ **L**iskov substitution principle
+ **I**ntegration Segregation principle
+ **D**ependency Inversion principle

Checkout the links for the more detalied examples.   
The specific is very hard to pin point. Often time, we can apply most of the principles, in similar or one modification, strategy pattern. However, it is important to keep in mind, if we have satisfied all of SOLID principles. 

1. #### Single Responsibility Principle ####

    + [Source one](https://dotnetcodr.com/2013/08/12/solid-design-principles-in-net-the-single-responsibility-principle/)
    + [Source two, same guy](https://dotnetcodr.com/2015/04/27/solid-principles-in-net-revisited-part-2-single-responsibility-principle/)  
    Class or method should be responsibile for one thing. Think it this way, we do not want to change class or method to change implemenation details. 

    ```csharp
    // pseudo code
    public class Order {

        public bool Checkout(List<Product> products){

            //charge a customer with credit card

            //notify customer with email

            //complete order
            return true;
        }

        public void ChargeMoney(){
            // charge money with credit card details
        }

        public void SendEmailNotification(){
            // open email client and send email to customer email
        }
    }
    ```

    Problem
    + what if we need to change money charging from credit card to cash or online coupons, bitcoins
    + what if sending notification is not necessary or we are sending from different email client.
    + what if other class want to use sending notification method? - have to copy whole method to different class.   

    We will have to change `Order` class, if we are to implement these changes. Changes that are not related to Order class. `Order` class just should concentrate on ordering the products.

    **Solution**: Separate `ChargeMoney` and `SendEmailNotification` methods to interface and implement their method. The details will be carried by whoever is handling the details charging or sending notification. We should not worry about it.

1. #### Open closed principle ####

    [good article](http://joelabrahamsson.com/a-simple-example-of-the-openclosed-principle/)

    Open for extension, closed for modification. We do not want the code that has business logic to be modified all the time. We can change the behavior without modifying the original code. Abstraction of `SendingEmail` or `PaymentModule` with **interface** or **abstract** class, we do not have to worry about particular detail of payments or sending email. If in a future, we want to change or add new system, we can simply point to which method we want to use. We can follow this principle using polymorphism or applying interface.  

    Open Closed principle and Single Responsibility principle are very closely related in that both advocate thining the class on method to focus on one particular use case. They don't want classes or methods to be doing too many things in one particular place.

    ```csharp
    /// anti-pattern
    public double Area(object[] shapes)
    {
        double area = 0;
        foreach (var shape in shapes)
        {
            if (shape is Rectangle)
            {
                Rectangle rectangle = (Rectangle) shape;
                area += rectangle.Width*rectangle.Height;
            }
            else
            {
                Circle circle = (Circle)shape;
                area += circle.Radius * circle.Radius * Math.PI;
            }
        }

        return area;
    }
    /// have to change this method, if we want to add another shape in the mix
    /// Solution
    public abstract class Shape
    {
        public abstract double Area();
    }

    public class Rectangle : Shape
    {
        public double Width { get; set; }
        public dobule Height { get; set; }
        public override double Area()
        {
            return Width * Height;
        }
    }

    public class Circle :Shape 
    {
        public double Radius { get; set; }
        public override double Area()
        {
            return Math.PI*Radius*Radius;
        }
    }
    /// here comes the best part
    public double Area(Shape[] shapes)
    {
        double area = 0;
        foreach(var shape in shapes)
        {
            area += shape.Area(); 
        }
        return area;
    }
    /// now we can add another class, say ellipse, by applying Shape abstract class, we do not have to modify Area class at all. :)
    ```

1. #### Liskov Substitution ####

    [Read the intro from this article](https://dotnetcodr.com/2013/08/19/solid-design-principles-in-net-the-liskov-substitution-principle/)  
    Derived class should be substituable for their base class. i.e, child class should be able to replace the parent class, while performing as expected. In other words, the user of base class should function properly if derivative of that base class is passed to it.   

    When inheritance is used, we define as IS-A relationship. With this principle, it should be IS-SUBSTITUABLE-FOR relationship. Say in above example, Circle is a Shape. But if Cirlce **Area** method returned `throw NotImplementatedException()`. Then it is not really replaceable/substitutable, is it?  

    The example in the linked pdf is revealing. The classic example of trying to make ellipse behave as ellipse. Another example: Square is a Rectangle. If we have `public class Square: Rectangle`. Because square length and witdh is equal, we set last width or length provided as a length of square, to behave make it behave as Square. However, when we replace Rectangle with Square, it will try to reset a new length as width and the Area will return wrong calculation.   

1. #### Interface Segregation principle ####

    + no client should be forced to depend on methods it does not use. Reduce interface to smallest required implementation. Imagine you need to implement just some of the interface methods but not all. In this situation, interface, is not following this principle.

    + Demo

        ```csharp
        //suppose store sells two producsts DVD and BluRay
        public interface IProduct 
        {
            decimal Price { get; set; }
            double Weight { get; set; }
            int Stock { get; set; }
            int AgeLimit { get; set; }
            int RunningTime { get; set; }
        }
        //then applied to product class
        public class DVD: IProduct {...}
        public class BluRay: IProdcut {...}
        //what if we want to sell T-shirt now
        /*
        If we apply IProduct to it, TShirt do not have AgeLimit or RunningTime property, also TShirt have its own unique property like Size: S, M, L.
        If we choose to implement IProduct as it is, then DVD, and BluRay class should be modified with method that does nothing.
        */
        //Break the interface to smaller ones
        public interface IProduct
        {
            decimal Price { get; set; }
            double Weight { get; set; }
            int Stock { get; set; }
        }
        public interface IMovie: IProduct
        {
            int RunningTime { get; set; }
        }
        //Now we can implement IProduct on both Tshirt and Movies
        public class BluRay: IMovie
        {
            public decimal Price { get; set; }
            public double Weight { get; set; }
            public int Stock { get; set; }
            public int Runningtime { get; set;}
        }
        public class TShirt: IProduct {...}
        ```

1. #### Dependency Inversion Principle (DIP) ####

    + [This article has lots of graphs](http://joelabrahamsson.com/inversion-of-control-an-introduction-with-examples-in-net/)
    + [Martin Fowler on IoC Containers](https://martinfowler.com/articles/injection.html)
    + helps to decouple your code by ensuring that you depend on abstractions rather than concrete implementations. Dependecy Injection(DI) is an implementation of this principle.
    + DIP : programming to abstractions so that consuming classes can depend on those abstractions rather than low-level implementations
    + DI: act of supplying all classes that a service needs rather than letting the service obtain its concrete dependecies.
    + Inversion of Control (IoC) : meant a programming style where a framework or runtime controlled the programme flow. Now, all this terms are interchangeably used.
    + > The frequency of the 'new' keyword in your code is a rough estimate of the degree of coupling in your object structure.
    + Types of DI
        + Constructor injection

            ```csharp
            private IProductRepository _productRepository;
            public ProductService(IProductRepository productRepository){
                _productRepository = productRepository;
            }
            ```

        + Property/Setter Injection
            + used when your class had a good local default for a dependency but still want to enable clients to override that default.

            ```csharp
            //complete version; does not need to be this complex
            public IPRoductRepository ProductRepository
            {
                get
                {
                    if(_productRepository == null)
                    {
                        _productRepository = new ProductRepository();
                    }
                    return _productRepository;
                }
                set
                {
                    if(value == null) throw new ArgumentNullException("ProductRepo");
                    if(_productRepository != null) throw new InvalidOperationException("_repo already exists" );
                    _productRepository = value;
                }
            }
            ```

        + Method injection
            + used when we can to ensure that we can inject a different implementation every time the dependency is used

            ```csharp
            public IEnumerable<Product> GetProducts(IProductionDiscountStrategy productDiscount)
            {
                IEnumerable<Product> productsFromDataStore = _productRepository.FindAll();
                foreach(Product p in productsFromDataStore)
                {
                    p.AdjustPrice(productDiscount);
                }
                return productsFromDataStore;
            }
            ```

        + ambient context
            + need more explanation here. However, constructor injection is preferred than this.
    + Factory pattern (static, abstract, concrete) is anti-pattern way of implementing DI
    + Service Locator
        + serve instances of services when consumers request them.
        + most common implementation of the pattern introduces a static factory
        + anti-pattern because most client class won't know about dependency
        + Martin Fowler
        > The key difference is that with a Service Locator every user of a service has a dependency so the locator. The locator can hide dependecis to other implementations, but you do need to see the locator. So the decision between locator and injector depends on whether that depedency is a problem.
        + Also, we can use both on technqiue in same project, depending on the class.
    + there seems to be confusion regarding the terms: Service Locator, IoC Container, looks like it mean same!