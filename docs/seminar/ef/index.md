# Entity Framework

The goal of the seminar is to practice writing Linq queries and working with Entity Framework.

!!! important "Entity Framework ~~Core~~"
    In this seminar, we are using Entity Framework available in the .NET Framework and not the cross-platform Core version. The usage of Linq is very similar, if not identical in both technologies. The visual code generation technology, however, is only available in the .NET Framework and Entity Framework.

## Pre-requisites

Required tools to complete the tasks:

- Microsoft Visual Studio 2015/2017/2019 (_not_ VS Code)
- Microsoft SQL Server (LocalDB or Express edition)
- SQL Server Management Studio
- Database initialization script: [mssql.sql](https://raw.githubusercontent.com/bmeviauac01/adatvezerelt/master/docs/db/mssql.sql)

Recommended to review:

- C# language
- Entity Framework and Linq

## How to work during the seminar

The exercises are solved together with the instructor. A few exercises we can try to solve by ourselves and then discuss the results. The final exercise is individual work if time permits.

!!! info ""
    This guide summarizes and explains the behavior. Before looking at these provided answers, we should think first!

## Exercise 0: Create/check the database

The database resides on each machine; thus, the database you created previously might not be available. First, check if your database exists, and if it does not, create and initialize it. (See the instructions [in the first seminar material](../transactions/index.md).)

## Exercise 1: Create a project and map the database

Let us create a new C# console application in Visual Studio. In VS 2019 search for "console framework"; this will yield the project template we are looking for.

![VS projext type](images/vs-create-project.png)

!!! important ""
    Do **not** create a .NET _Core_ application because it does not support the visual _Database First_ code generation we will use.

    Mind the project language: it shall be C#.

Create a new project; you may work in directory `c:\work`.

1. Add a new _ADO.NET Entity Data Model_ to the project.

    - In Solution Explorer right-click the project / _Add / New Item / Data / ADO.NET Entity Data Model_. At the bottom of the dialog, use the name `DataDrivenEntities`.
    - The model shall be created based on an existing database: in the wizard, choose the "EF designer from database" option.
    - Specify the connection to the database. Create a new connection and save the connection string into the app config file.
        - _Data source_: Microsoft SQL Server
        - _Server name_: `(localdb)\mssqllocaldb`
        - _Select or enter database name_: let us chose our database
        - _Save connection settings in App.Config_:
            - yes (check)
            - use the name `DataDrivenEntities` (this will be the name of the DbContext class)
    - Use Entity Framework 6.0 mapping.
    - Map all tables: make a check next to the _Tables_ folder.
    - _Model namespace_: e.g. `DataDrivenEntitiesModel`

1. Wait for the code generation. If VS asks about running a "template" let us allow it.

1. Locate the _connection string_ in `app.config` and check its contents.

    !!! note "`app.config`"
        It is advisable to put configuration, such as the _connection string_ here, because the database access information may be different for each installation of an application. If the connection string is hard-wired into the source code, the application needs to be recompiled to change it. However, the `app.config` file is part of the application next to the executable is an editable xml format.

1. Open the EF data model (double-click in the Solution Explorer). Let us examine the entities and the connections.

    - The model can be changed using the _Entity Data Model Browser_ and _Entity Data Model Mapping Details_ windows (if not visible open them through _View_ / _Other windows_).
    - Let us make a few corrections in the generated names:

        - Customer.CustomerSite1 -> **.Sites**
        - CustomerSite.Customer1 -> **.MainCustomer**
        - Order.OrderItem -> .OrderItem&#8203;**s**
        - Product.OrderItem -> .OrderItem&#8203;**s**
        - VAT.Product -> .Product&#8203;**s**
        - Category.Product -> .Product&#8203;**s**

    Save the model using Ctrl-S.

1. Let us examine the C# source code of the _DbContext_ any one of the entity classes. In _Solution Explorer_ expand the EDM model file, and the C# source files will be listed underneath.

    !!! note ""
        You should _not_ make changes to these files, as these are generated by the EDM model. Any change we make is lost when the EDM is re-generated. We may note, however, that all classes are declared as `partial`; thus we can extend them by creating new source files and using the same namespace and class name.

## Exercise 2: Queries

In the following exercises, write C# code using Linq to Entities. The results should be printed to console.

You can check the SQL query generated in runtime: hover over the IQueryable variable once the iteration of the list is underway; it will show you the generated command.

1. List the names and the amount of stock of all products that we have more than 30 in stock!

1. List the products that have been ordered at least twice!

1. List the orders that have a total value of at least 30.000! For each order, print the customer name and list all items of the order (with the product name, amount, and price).

1. Find the most expensive product!

1. List all customer record pairs that have their main site of business in the same city. Each pair should only be listed once.

??? example "Solution"
    ```csharp
    Console.WriteLine("***** Exercise two *****");
    using (var db = new DataDrivenEntities())
    {
        // 2.1
        Console.WriteLine("\t2.1:");
        var qProductStock = from p in db.Product
                            where p.Stock > 30
                            select p;
        // alternative syntax
        // var qProductStock = db.Product.Where(p => p.Stock > 30);
        foreach (var p in qProductStock)
            Console.WriteLine("\t\tName={0}\tStock={1}", p.Name, p.Stock);

        // 2.2
        Console.WriteLine("\t2.2:");
        var qProductOrder = from p in db.Product
                            where p.OrderItems.Count >= 2
                            select p;
        // alternative syntax
        // var qProductOrder = db.Product.Where(p => p.OrderItem.Count >= 2);
        foreach (var p in qProductOrder)
            Console.WriteLine("\t\tName={0}", p.Name);

        // 2.3
        Console.WriteLine("\t2.3:");
        var qOrderTotal = from o in db.Order
                            where o.OrderItems.Sum(oi => oi.Amount * oi.Price) > 30000
                            select o;
        foreach (var o in qOrderTotal)
        {
            Console.WriteLine("\t\tName={0}", o.CustomerSite.MainCustomer.Name);
            foreach (var oi in o.OrderItems)
                Console.WriteLine("\t\t\tProduct={0}\tPrice={1}\tAmount={2}", oi.Product.Name, oi.Price, oi.Amount);
        }

        // 2.3 alternative solution
        // A new namespace import is needed; copy the following line to the top of this file!
        // using System.Data.Entity;

        // Generates a single query and populates the Navigation Properties too
        Console.WriteLine("\tc 2.3 alternative solution:");
        var qOrderTotal2 =
            from o in db.Order
                .Include(o => o.OrderItems)                          // or .Include("OrderItem")
                .Include(o => o.OrderItems.Select(oi => oi.Product)) // or .Include("OrderItem.Product")
                .Include(o => o.CustomerSite)                        // or .Include("CustomerSite")
                .Include(o => o.CustomerSite.MainCustomer)           // or .Include("CustomerSite.Customer")
            where o.OrderItems.Sum(oi => oi.Amount * oi.Price) > 30000
            select o;

        foreach (var o in qOrderTotal2)
        {
            Console.WriteLine("\t\tName={0}", o.CustomerSite.MainCustomer.Name);
            foreach (var oi in o.OrderItems)
                Console.WriteLine("\t\t\tProduct={0}\tPrice={1}\tAmount={2}", oi.Product.Name, oi.Price, oi.Amount);
        }

        // 2.4
        Console.WriteLine("\t2.4:");
        var qPriceMax = from p in db.Product
                        where p.Price == db.Product.Max(a => a.Price)
                        select p;
        // alternative syntax
        // var qPriceMax = db.Product.Where(p => p.Price == ctx.Product.Max(p2 => p2.Price));
        foreach (var t in qPriceMax)
            Console.WriteLine("\t\tName={0}\tPrice={1}", t.Name, t.Price);

        // 2.5
        Console.WriteLine("\t2.5:");
        var qJoin = from s1 in db.CustomerSite
                    join s2 in db.CustomerSite on s1.City equals s2.City
                    where s1.CustomerID > s2.CustomerID
                    select new { c1 = s1.MainCustomer, c2 = s2.MainCustomer };
        foreach (var v in qJoin)
            Console.WriteLine("\t\tCustomer 1={0}\tCustomer 2={1}", v.c1.Name, v.c2.Name);
    }
    ```

## Exercise 3: Data modification

The DbContext can also be used to modify the database.

1. Write C# code that increases the price of all products in category "LEGO" by 10 percent!

1. Create a new category named _Expensive toys_ and move all products here that cost more than 8000!

??? example "Solution"
    ```csharp
    Console.WriteLine("***** Exercise three *****");
    using (var db = new DataDrivenEntities())
    {
        // 3.1
        Console.WriteLine("\t3.1:");
        var qProductsLego = from p in db.Product
                            where p.Category.Name == "LEGO"
                            select p;
        // alternative syntax
        // var qProductsLego = db.Product.Where(p => p.Category.Name == "LEGO");

        Console.WriteLine("\tBefore change:");
        foreach (var p in qProductsLego)
        {
            Console.WriteLine("\t\t\tName={0}\tStock={1}\tPrice={2}", p.Name, p.Stock, p.Price);
            p.Price = 1.1 * p.Price;
        }

        db.SaveChanges();

        qProductsLego = from p in db.Product
                        where p.Category.Name == "LEGO"
                        select p;
        Console.WriteLine("\tAfter change:");
        foreach (var p in qProductsLego)
            Console.WriteLine("\t\t\tName={0}\tStock={1}\tPrice={2}", p.Name, p.Stock, p.Price);

        // 3.2
        Console.WriteLine("\t3.2:");
        Category categoryExpensiveToys = (from c in db.Category
                                            where c.Name == "Expensive toys"
                                            select c).SingleOrDefault();
        // alternative syntax
        // var categoryExpensiveToys = db.Category.Where(c => c.Name == "Expensive Toys")
        //                                        .SingleOrDefault();

        if (categoryExpensiveToys == null)
        {
            categoryExpensiveToys = new Category { Name = "Expensive toys" };

            // The following line is not always necessary. If there is any product assigned to this
            // new category, when saving the changes, a new category record will automatically be
            // created. If we add this explicitly though, it better reflects our way of thinking,
            // and if there are no assigned products, the new category record is created regardless.
            db.Category.Add(categoryExpensiveToys);
        }

        var qProductExpensive = from p in db.Product
                                where p.Price > 8000
                                select p;

        foreach (var p in qProductExpensive)
            p.Category = categoryExpensiveToys;
        db.SaveChanges();

        qProductExpensive = from p in db.Product
                            where p.Category.Name == "Expensive toys"
                            select p;

        foreach (var t in qProductExpensive)
            Console.WriteLine("\t\tName={0}\tPrice={1}", t.Name, t.Price);
    }
    ```

## Exercise 4: Using stored procedures

Stored procedures can be mapped into the EDM model either as a method on the DbContext or an entity's operation.

!!! note "Procedure mapping in the EDM"
    The mapping properties of the stored procedure (e.g., the return value) usually has to be manually altered using the _Entity Data Model Browser_: find the _Function Import_ and open its properties.

    ![Mapping properties of a stored procedure](images/vs-storedproc-mapping.png)

1. Create a new stored procedure that can be used to register a new payment method. Map this stored procedure as the insert operation of the `PaymentMethod` entity!

    - Create the stored procedure using SQL Management Studio.

        ```sql
        CREATE PROCEDURE CreateNewPaymentMethod
        (
        @Method nvarchar(20),
        @Deadline int
        )
        AS
        insert into PaymentMethod
        values(@Method,@Deadline)
        select scope_identity() as NewId
        ```

    - Set this procedure as the `PaymentMethod` entity _insert_ operation.

        - Add the stored procedure to the EDM. In the EDM Browser right-click to open the context menu and use "Update model from database" to _Add_ the previously created procedure.
        - Save the model changes to generate the required C# code in the background.
        - Set this procedure on the `PaymentMethod` entity as the _insert_ operation: select the `PaymentMethod` entity in the EDM and in the _Mapping Details_ window change to the _Map Entity to Functions_ tab, then chose the procedure for _Insert_ operation. The return value shall be mapped to the _ID_ property. Save the model.

            ![Mapping procedure to entity](images/vs-insert-storedproc.png)

    - Test the behavior: in C# code, create a new `PaymentMethod` entity and add it to the appropriate list of the DbContext using `Add`. Verify the creation of the new record in the database.

1. Create a stored procedure that lists all products that have been sold more than a specified amount of times. Call this method from C# code!

    - Create the stored procedure with the T-SQL command below.

        ```sql
        CREATE PROCEDURE dbo.PopularProducts (
        @MinAmount int = 10
        )
        AS
        SELECT Product.* FROM Product INNER JOIN
        (
        SELECT OrderItem.ProductID
        FROM OrderItem
        GROUP BY OrderItem.ProductID
        HAVING SUM(OrderItem.Amount) > @MinAmount
        ) a ON Product.ID = a.ProductID
        ```

    - Import the procedure into the EDM. Open the function properties (in the _EDM Model Browser_ double click _function_) and set return value as type `Product`. Save the model changes.

        ![Map return value of a stored procedure](images/vs-storedproc-mapping.png)

    - Use the new method available on the DbContext class to call this method and print the returned product names to console!

??? example "Solution"
    ```csharp
    Console.WriteLine("***** Exercise four *****");
    using (var db = new DataDrivenEntities())
    {
        // 4.3
        Console.WriteLine("\t4.3:");

        var pm = new PaymentMethod
        {
            Method = "Apple pay",
            Deadline = 99999
        };

        db.PaymentMethod.Add(pm);
        db.SaveChanges();

        // 4.6
        Console.WriteLine("\t4.6:");
        var qPopularProducts = db.PopularProducts(5);
        foreach (var p in qPopularProducts)
            Console.WriteLine("\t\tName={0}\tStock={1}\tPrice={2}", p.Name, p.Stock, p.Price);
    }
    ```
