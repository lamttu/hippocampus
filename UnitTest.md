# Unit Test

## Properties of a good Unit Test

- Automated and repeatable
- Easy to implement
- Relevant tomorrow
- Quick
- Consistent (same inputs return same results)
- Independent of other tests
- When the test fails, it's easy to determine the problem

## Parameterised Test

- NUnit: `[TestCase(param1, param2)]`
- xUnit: `[Theory][InlineData(param1, param2)]`

## Name your test

`MethodUnderTest_Scenario_Behavior()`

## SetUp and TearDown

Be careful while using these because it means you'll have to read code at multiple places (less readability)

- SetUp: Use factory methods instead
- You should almost _never_ use TearDown

## Exception

Don't use [ExpectedException] because you can't control which line throws the exception

## Breaking dependencies

These are the most notable ways

### **1. Receive an interface at the constructor level and save it in a field for later use**

```
public class LogAnalyzer
{
    private ILogger _logger;

    public LogAnalyzer(ILogger logger)
    {
        _logger = logger;
    }
}
```

This means it's compulsory to inject the ILogger object when you want to initialise a LogAnalyser

#### Caveats

If a class needs too many dependencies, it would become a hassle and make the code less readable
In these cases you might want to use IoC, but that's an entirely different beast.

### **2. Receive an interface as a property get or set and save it in a field for later use**

```
public class LogAnalyzer
{
    private ILogger logger;

    public ILogger Logger
    {
        get; set;
    }
}
```

More relaxed compared to constructor injection -> basically saying this property is not mandatory

### **3. Receive an interface just before the call in the method under test using one of the following:**

- A parameter to the method (parameter injection)
- A factory class

```
public class LogAnalyzer
{
    private IExtensionManager manager;

    public LogAnalyzer()
    {
        manager = ExtensionManagerFactory.Create();
    }

}

public class ExtensionManagerFactory
{
    private static IExtensionManager m_manager
                    =new FileExtensionManager();

    public static IExtensionManager Create()
    {
        return m_manager;
    }

    public static void SetManagerInstance(IExtensionManager value)
    {
        m_manager = value;
    }
}

[Test]
public void IsValidFileName_NameShorterThan6CharsButSupportedExtension_ReturnsFalse()
{
    //setup the stub to use, make sure it returns true
    ExtensionManagerFactory.SetManagerInstance(myFakeManager);

    //create analyzer and inject stub
    LogAnalyzerConstructorStub log =
        new LogAnalyzerConstructorStub();

    //Assert
}
```

Quite easy to use since it doesn't require changing the constructor of the class.
However, you need to be mindful of who calls the factory and when (potentially make the test stateful if youre not careful??)

- A local factory method aka **_Extract and Override_**
  1. Make `GetManager()` a virtual method so that a child class can override this method
  2. Create a `TestableLogAnalyser` that extends `LogAnalyser`
  3. Inject an `IExtensionManager` in the constructor of `TestableLogAnalyser`
  4. In `TestableLogAnalyser`, override `GetManager()` to return the injected `IExtensionManager`

Extract and Override is good if you’d like to simulate return values or simulate whole interfaces as return values but not good for checking interactions between objects.

- Variations on the preceding techniques

If you're worried about adding constructors, etc. You can

- make the method `internal` and only expose it to the test `[assembly:InternalsVisibleTo("AOUT.CH3.Logan.Tests")]` in AssemblyInfo.cs
- or using `[Conditional ("DEBUG")]`

BEWARE OF MAKING YOUR CODE INTO A SPAGHETTI DISH

## Different types of testing

### 1. State-based

Assert against the change in state of the system. In the case of The Art of Unit Testing, this seems synonymous to behaviour testing

### 2. Value-based

Assert against the return value

### 3. Interaction testing

Assert to verify how an object invokes methods to other objects.

**_Always_** choose to use interaction testing only as the last option.

# The things you fake in your tests

> When you can't test something, add a layer to wrap that something, then mimic that layer in your test

## Stub

A _stub_ is a controllable replacement for an existing dependency in the system. By using a stub, you can test your code without dealing with the dependency directly.

You do **_not_** assert against a stub

## Mock

A _mock_ is a fake object in the system that decides whether the unit test has passed or failed. It does so by verifying whether the object under test called the fake object as expected.

A mock might double as a stub when you need to use a mock object that has a function that returns a value.

There’s usually no more than one mock per test. If there are many mocks, it means you're testing many things at once.

## Fake

A _fake_ is a generic term that can be used to describe either a stub or a mock object, because they both look like the real object. Whether a fake is a stub or a mock depends on how it’s used in the current test. If it’s used to check an interaction (asserted against), it’s a mock object. Otherwise, it’s a stub.

## The difference between mocks and stubs

Stubs can’t fail tests. Mocks can.
When using a stub, the assert is performed on the class under test. The stub aids in making sure the test runs smoothly.

test <---- assert ----> sut <----- communicate-------> stub

On the other hand, the test will use a mock object to verify whether or not the test failed

sut <----- communicate-------> mock <---- assert ----> test

```
[Test]
public void Analyze_TooShortFileName_CallsWebService()
{
    MockService mockService = new MockService();
    LogAnalyzer log = new LogAnalyzer(mockService);
    string tooShortFileName="abc.ext";
    log.Analyze(tooShortFileName);

    Assert.AreEqual("Filename too short:abc.ext",mockService.LastError); <---- assert against mockService
}
```

# Isolation framework

A reusable library that can create and configure fake objects at runtime. These objects are referred to as _dynamic stubs_ and _dynamic mocks_.

Ex: Moq

## Constrained frameworks

There are things they can't fake. They are constrained by the compiler and can't fake static methods, nonvirtual methods, nonpublic methods, and more.

Ex: Moq, RhinoMock, NMock, NSubstitute

## Unconstrained frameworks

Unconstrained frameworks don’t generate and compile code at runtime that inherits from other code. They can do anything.

Ex: Typemock Isolator, JustMock, and Moles

## Watch out for

1. Unreadable test code
2. Verifying the wrong thing
3. Having more than one mock per test
4. Overspecifying the test
   - If your test have too many Verify() call, it will become very fragile.
   - Use stubs instead of mocks whenever you can
   - Avoid using stubs as mocks

Use a stub only for faking return values into the program under test or to throw exceptions. Don’t verify that methods were called on stubs.

Use a mock only for verifying that some method was called on it, but don’t use it to return values into your program under test.

# Test hiearchy and organisation

- Separate unit tests and integration tests
- Source control your tests

You should be able to:

1. Look at a project and find all tests - ProjectName.UnitTests
2. Look at a method and find all tests - Test method should start w part of the method name
3. Look at a class and find all tests - LogAnalyser.UnitTests.cs
   - If your methods are complex, consider one test class per feature (could be as small as a method)

## Test class inheritance

Consider using an abstract `TemplateTestClass` to ensure people always test some required methods

> A template test pattern ensures that developers don’t forget important tests. The base class contains abstract tests that derived classes must implement.

# Pillars of good tests

- Rename or refactor tests for better readability
- Avoid logic in tests
  - No switch, if, or else statements
  - No foreach, for, or while loops
  - No try-catch
- Test only one concern
- Maintain code coverage (this doesn't replace code reviews)
- DRY (with a helper method instead of SetUp)
- Test should be isolated from other tests

## Tests isolation smell

1. Tests have to be executed in a specific order
2. Tests calling other tests
3. Shared state corruption
   - Tests touch shared resources (db, file systems, etc)
   - Tests don't set up initial state but expect the state to be there
4. Intergation tests sharing resources without rollback

## Avoid overspecification

> Unit tests should be testing the public contract and public functionality of an object.

- Avoid using multiple mocks
- Avoid using stubs as mocks
- Avoid exact string matches or specific order when it's not required

## Procedure to add a missing test

1. Comment out the code you think isn’t being covered
2. Run all tests
3. If all the tests pass, you’re missing a test or are testing the wrong thing.
4. Add in the missing test (keep the code commented). Run the test and see if it fails to confirm that the test targets the commented code
5. Uncomment the code and run tests
6. Tests pass!! You've detected and added a missing test

## Private methods

No private method exists without a reason. When you test a private method, you’re testing against a contract internal to the system, which may well change.

For testing purposes, the public contract (the overall functionality) is all that you need to care about. Testing the functionality of private methods may lead to breaking tests, even though the overall functionality is correct.

> With TDD, you usually write tests against methods that are public, and those public methods are later refactored into calling smaller, private methods. All the while, the tests against the public methods continue to pass.

### **Then how to test them??**

Find the public use case in the system that will exercise the private method

If you test only the private method and it works, that doesn’t mean that the rest of the system is using this private method correctly or handles the results it provides correctly.

# Testing legacy code

## Where do you start adding tests?

Consider these factors:

- **Logical complexity**: the amount of logic (ifs, switch cases, recursion)
- **Dependency level**: how many dependencies this component has
- **Priority**: of the component to the project

Give these components a ranking from 1 (low) to 10 (high)

### **Feasibility table**

| Component     | Logical complexity | Dependency level | Priority | Notes                                                                                                                                   |
| ------------- | ------------------ | ---------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Utils         | 6                  | 1                | 5        | Few dependencies but contains a lot of logic. It will be easy to test, and it provides lots of value.                                   |
| Person        | 2                  | 1                | 1        | A data-holder class with little logic and no dependencies. There’s some (small) real value in testing this.                             |
| TextParser    | 8                  | 4                | 6        | Lots of logic and lots of dependencies, high-priority. Testing this will provide lots of value but will also be hard and time consuming |
| ConfigManager | 1                  | 6                | 1        | Little logic but many dependencies. Testing it will provide little value to the project and will also be hard and time consuming.       |

                 |            |
                 | Utils      | TextParser
                 |            |
                 | ___________|_____________
                 |            |
        logic    |            |
                 | Person     |
                 |            |        ConfigManager
                 |
                 |___________________________
                           dependencies

**Choose:**

- complex and easy to test (top left)
- complex and hard to test (top right)

For team with little experience, you can start w easy components first but beware at time goes by, only hard components will be left. (The team need to upskill in time for this)

For team with more experience, hard-first strategy will be possible. Every time you bring a component under test and refactor it to make it more testable, you may also be solving testability issues for the dependencies it uses or for other components and improve things for other parts of the system. So testing gets easier and easier

## Write integration test before refactoring

Those tests set the “original” working behavior of the system

**Process**

1. Add one or more integration tests (no mocks or stubs) to the system to prove the original system works as needed.
2. Refactor or add a failing test for the feature you’re trying to add to the system.
3. Refactor and change the system in small chunks, and run the integration tests as often as you can, to see if you break something.

# Database testing

- Use integration tests for your data layer
- Use `TransactionScope` to roll back data

```
[TestFixture]
public class TrannsactionScopeTests

{
    private TransactionScope trans = null;
    [SetUp]
    public void SetUp()
    {
        trans = new TransactionScope(TransactionScopeOption.Required);
    }
    [TearDown]
    public void TearDown()
    {
        trans.Dispose();
    }

   [Test]
    public void TestServicedSameTransaction()
    {
        MySimpleClass c = new MySimpleClass();

        long id = c.InsertCategoryStandard("whatever");
        long id2 = c.InsertCategoryStandard("whatever");
        Console.WriteLine("Got id of " + id);
        Console.WriteLine("Got id of " + id2);
        Assert.AreNotEqual(id, id2);
    }

}
```

## In-memory database options

If the in-memory database has a different engine from the production database, there are chances that the behaviour is different between tests and production.

Pick the in-memory database engine has the same features and logic embedded in it

# Extras

If you're doing multiple asserts on the same "feature" attributes, consider making these attributes into a class so that you can have one assert. However it only works when

- it's easy to create the expected object
- you know all the properties of the object
- you want to test all properties

Your test is now tied to this object so it will also be less robust for future changes.

You can gut the content of a static method and put it into a normal method. The static method then will direct trafic to the normal method and we can mock the normal method easily.
