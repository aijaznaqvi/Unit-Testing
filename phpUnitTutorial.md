# Automated Unit Testing with PHPUnit
## Unit testing
It tests the smallest unit of functionality. From a developer's point of view, its focus is to make sure one function does what it is supposed to do. Thus it should have minimum or no dependence on other function/class. It should be done in memory, which means it should not connect to database, access the network or use the file system and so on. Unit testing should be as simple as possible.

## Introduction to PHPUnit
PHPUnit is currently the most popular PHP unit testing framework for creating a test suite to automate testing of functions and classes. Besides providing features such as mocking objects, it provides code coverage analysis, logging and tons of other powerful features.

## Installing PHPUnit
Add this line in your composer.json file:

```
{
    "require-dev": {
        "phpunit/phpunit": "3.7.14"
    }
}
```
To make Composer download the new libraries simply run `composer update`. The project files will live within the `phpUnitTutorial` folder.

## Running PHPUnit
To run PHPUnit, type the command below in your terminal
```
./vendor/bin/phpunit
```
This will print all options available to you.

To see PHPUnit version:
```
phpunit --version
```

## Setting Up phpunit.xml
Running PHPUnit will go through your tests using built-in defaults. You can override many defaults via the command line, but there is a better way: the `phpunit.xml` configuration file.

Create a `tests` folder in the project and in the `tests` folder create a `phpunit.xml` with the following contents:

```
<?xml version="1.0" encoding="UTF-8"?>
<phpunit colors="true">
    <testsuites>
        <testsuite name="Application Test Suite">
            <directory>./unit/</directory>
        </testsuite>
    </testsuites>
</phpunit>
```
This is a simple configuration file, but it sets two important options:

`colors="true"` makes sure your test results are in color, and

`<directory>./unit/</directory>` tells PHPUnit where your tests will be located, so you don’t have to manually tell it every time you run your tests.

If you have not created this folder, so do so now. Your file structure should look like:

```
phpUnitTutorial/composer.json
phpUnitTutorial/
phpUnitTutorial/tests/
phpUnitTutorial/tests/unit/
phpUnitTutorial/vendor/
```
All your application’s tests should go within `phpUnitTutorial/tests/unit`.

## Conventions
### File structure and filenames
The test files must match the file they are testing, with `Test` appended. For example, if we had the following code:
```
./phpUnitTutorial/models/Foo.php
./phpUnitTutorial/models/Bar.php
./phpUnitTutorial/controllers/BazController.php
```
Our tests would be structured as:
```
./phpUnitTutorial/tests/unit/models/FooTest.php
./phpUnitTutorial/tests/unit/models/BarTest.php
./phpUnitTutorial/tests/unit/controllers/BazControllerTest.php
```
### Classnames
Classnames are exactly the same as filenames. Whatever you have named your file should be the name of your class - which should be true for your non-test code anyway!

### Method (test) names
Your test method names should start with `test`, in lower case. Method names should be descriptive of what is being tested, and should include the name of the method being tested.

For example, if you are testing a method called `verifyAccount()`, and in one unit test you want to test that the password matches, you would name your test `testVerifyAccountMatchesPasswordGiven()`.

### Methods must be public
PHPUnit is unable to run tests that are either protected or private - they must be public.

### Extends PHPUnit
Your classes must extend the PHPUnit_Framework_TestCase class, or extend a class that does.

## Creating test suite
A test suite checks every function of your class. A test suite is normal PHP class inherited from PHPUnit_TestCase containing test functions, identified by a leading 'test' in the function name. In the test function an expected value has to be compared with the result of the function to test. The result of this compare must delegate to a function of the `assert*()`-family, which decides if a function passes or fails the test.

## First Test
Create a new file at `./phpUnitTutorial/tests/Test1.php`:
```
<?php
namespace phpUnitTutorial\Test;

class Test1 extends \PHPUnit_Framework_TestCase
{
    //
}
```
create a method `testTrueIsTrue`.
```
<?php
public function testTrueIsTrue()
{
    $foo = true;
    $this->assertTrue($foo);
}
```

## Assertions
An assertion verifies a statement made equals true.

In the above example, we asserted that `true` will assert to `true` ( if `(true == true)` ).

If we assert that `false` is `true` ( if `(false == true)` ), we would get a failing test:
```
<?php
public function testTrueIsTrue()
{
    $foo = false;
    $this->assertTrue($foo);
}
```

If we wanted to assert that `false` is `false` ( if `(false == false)` ):
```
<?php
public function testFalseIsFalse()
{
    $foo = false;
    $this->assertFalse($foo);
}
```
This test would pass because our assertion would be `true`, even if the assert method is called `assertFalse()`.

### Available assertions
PHPUnit ships with 90 assertions. They are accessible through `$this->assert*`. Most of the time you will only use `assertArrayHasKey()`, `assertEquals()`, `assertFalse()`, `assertSame()` and `assertTrue()`.

### Custom assertions
If you cannot find an assertion to perfectly match your requirement, define the method in your test class and start using it. If you ever need it in more than one class, move it to a parent class all your tests extend.

## First Useful Test
Let's create a very simple class called `Calculator`. And write a test for it.

Create a new file at `./phpUnitTutorial/Calculator.php` and copy the code below to the file. This `Calculator` class only has a `Add` function.:
```
<?php
namespace phpUnitTutorial;

class Calculator
{
 
    public function add($a, $b)
    {
        return $a + $b;
    }
 
}
```

Create the new test file at `phpUnitTutorial/tests/CalculatorTest.php`, and copy the code below to the file. We will explain each function in details.
```
<?php
namespace phpUnitTutorial\Test;

use phpUnitTutorial\Calculator;
 
class CalculatorTest extends \PHPUnit_Framework_TestCase
{
    private $calculator;
 
    protected function setUp()
    {
        $this->calculator = new Calculator();
    }
 
    protected function tearDown()
    {
        $this->calculator = NULL;
    }
 
    public function testAdd()
    {
        $result = $this->calculator->add(1, 2);
        $this->assertEquals(3, $result);
    }
 
}
```
* Include class `Calculator`. We have added a `use` statement so we do not have to use the full namespace when instantiating the object. This is the class that we are going to test against.
* `setUp()` is called before each test runs. Keep in mind, it runs before each test, which means, if you have another test function. It will run `setUp()` before it too.
* similar to `setUp()`, `tearDown()` is called after each test finishes.
* `testAdd()` is the test function for add function. PHPUnit will recognize all functions prefixed with test as a `test` function and run it automatically. This function is actually very straightforward, we first call `Calculator.add` function to calculate the value of 1 plus 2. Then we check if it returns correct value by using PHPUnit function `assertEquals`.

## The Command-Line Test Runner
Last part of job is to run PHPUnit and make sure it passes all tests. Navigate to the folder where you have created the test file and run command below from your terminal:
```
../vendor/bin/phpunit CalculatorTest.php
```
You should be able to see the successful message as below:
```
PHPUnit 4.8.19 by Sebastian Bergmann and contributors.

Runtime:        PHP 5.6.33 with Xdebug 2.2.5
Configuration:  /opt/lampp/htdocs/phpUnitTutorial/tests/phpunit.xml

.

Time: 238 ms, Memory: 11.00MB

OK (1 test, 1 assertion)
```
### Command-Line Options
Let's take a look at the command-line test runner's options:
#### --filter
Only runs tests whose name matches the given regular expression pattern. If the pattern is not enclosed in delimiters, PHPUnit will enclose the pattern in `/` delimiters.

Examples of valid filter patterns:
* `--filter 'TestNamespace\\TestCaseClass::testMethod'`
e.g., `../vendor/bin/phpunit --filter 'Lifecycle\\controllers\\HomeControllerTest::testActionSitemap'`

* `--filter 'TestNamespace\\TestCaseClass'`
e.g., `../vendor/bin/phpunit --filter 'Lifecycle\\controllers\\HomeControllerTest'`

* `--filter TestNamespace`
e.g., `../vendor/bin/phpunit --filter Lifecycle`

* `--filter TestCaseClass`
e.g., `../vendor/bin/phpunit --filter HomeControllerTest`

* `--filter testMethod`
e.g., `../vendor/bin/phpunit --filter testActionSitemap`

## Annotations
Annotations are special flags defined in your method docblocks:

```
<?php
/**
 * @annotationName Annotation value
 */
public function testFoo()
{
    //
}
```
### @dataProvider
When we write a function, we want to make sure it passes a series of edge cases. The same applies to tests. Which means we will need to write multiple tests to test the same function using different set of data. For instance, if we want to test our Calculator class using different data. Without data provider, we would have multiple tests as below:
```
<?php

namespace phpUnitTutorial\Test;

use phpUnitTutorial\Calculator;
 
class CalculatorTest extends \PHPUnit_Framework_TestCase
{
        private $calculator;
 
        protected function setUp()
        {
            $this->calculator = new Calculator();
        }
 
        protected function tearDown()
        {
            $this->calculator = NULL;
        }
 
        public function testAdd()
        {
            $result = $this->calculator->add(1, 2);
            $this->assertEquals(3, $result);
        }
 
        public function testAddWithZero()
        {
            $result = $this->calculator->add(0, 0);
            $this->assertEquals(0, $result);
        }
 
        public function testAddWithNegative()
        {
            $result = $this->calculator->add(-1, -1);
            $this->assertEquals(-2, $result);
        }
 
}
```

A data provider can be used to create multiple sets of information to be passed into a single test, removing the need to create multiple duplicate tests.

Instead of creating multiple test methods, you simply create a single method that accepts parameters corresponding to the data that is variable between tests, and create a data provider method to provide that data:

#### How to use data provider
A data provider method return an array of arrays or an object that implements the Iterator interface. The test method will be called with the contents of the array as its arguments.

Some key points as blow when using data provider:

* Data provider method must be public.
* Data provider return an array of a collection data.
* Test method use annotation(@dataProvider) to declare its data provider method.

Once we know the key points. It is actually quite straightforward to use data provider. First we create a new public method, which returns an array of a collection data as arguments of the test method.Then we add annotation to the test method to tell PHPUnit which method will provide arguments.

#### Test with @dataProvider
Let's modify our test above using data provider.
```
<?php

namespace phpUnitTutorial\Test;

use phpUnitTutorial\Calculator;
 
class CalculatorTest extends \PHPUnit_Framework_TestCase
{
    private $calculator;
 
    protected function setUp()
    {
        $this->calculator = new Calculator();
    }
 
    protected function tearDown()
    {
        $this->calculator = NULL;
    }
 
    public function addDataProvider() {
        return [
            [1,2,3],
            [0,0,0],
            [-1,-1,-2],
        ];
    }
 
    /**
     * @dataProvider addDataProvider
     */
    public function testAdd($a, $b, $expected)
    {
        $result = $this->calculator->add($a, $b);
        $this->assertEquals($expected, $result);
    }
 
}
```
Here we have created a single test and data provider method. Note the `@dataProvider` annotation that defines which method is providing data to the `testAdd()` test.

Now run our test again. It should pass. As you can see, we have utilized data provider to avoid code duplication. Instead of writing three test methods for essentially the same method. We now have only one test method.

### @author
The @author annotation allows to filter tests based on their authors.

### @codeCoverageIgnore, @codeCoverageIgnoreStart and @codeCoverageIgnoreEnd
The `@codeCoverageIgnore`, `@codeCoverageIgnoreStart` and `@codeCoverageIgnoreEnd` annotations can be used to exclude lines of code from the coverage analysis.
```
<?php
use PHPUnit\Framework\TestCase;

/**
 * @codeCoverageIgnore
 */
class Foo
{
    public function bar()
    {
    }
}

class Bar
{
    /**
     * @codeCoverageIgnore
     */
    public function foo()
    {
    }
}

if (false) {
    // @codeCoverageIgnoreStart
    print '*';
    // @codeCoverageIgnoreEnd
}

exit; // @codeCoverageIgnore
?>
```

### @covers
The @covers annotation can be used in the test code to specify which method(s) a test method wants to test:
```
/**
 * @covers BankAccount::getBalance
 */
public function testBalanceIsInitiallyZero()
{
    $this->assertSame(0, $this->ba->getBalance());
}
```

## Testing Exceptions
### Using the expectException() method
Use the expectException() method to test whether an exception is thrown by the code under test.
```
<?php
class ExceptionTest extends \PHPUnit_Framework_TestCase
{
    public function testException()
    {
        $this->expectException(InvalidArgumentException::class);
        // or for PHPUnit < 5.2
        // $this->setExpectedException(InvalidArgumentException::class);

        //...and then add your test code that generates the exception 
        exampleMethod($anInvalidArgument);
    }
}
```
### Using the @expectedException annotation
```
<?php
use PHPUnit\Framework\TestCase;

class ExceptionTest extends TestCase
{
    /**
     * @expectedException InvalidArgumentException
     */
    public function testException()
    {
    }
}
```
### Exception without annotations
Prior to PHPUnit 5.2, to test whether an exception is thrown, we would normally use annotation. For example, the test case below tests whether a RuntimeException exception is thrown during its execution.

```
/**
 * @expectedException \RuntimeException
 */
public function testDivide_runtime_exception_must_throw()
{
    $this->math->divide(1,0);
}
```
There are a few issues with annotation approach.

First we can't use the class constant in annotation comment. For example, if we expect the method to throw an exception that is in a particular namespace. We have to supply a full-qualified name of the exception class. The code as shown below demonstrates the point:

```
/**
 * @expectedException App\Model\Core\CoreException
 */
public function public function testDivide_runtime_exception_must_throw()()
{
    $this->math->divide(1,0);
}
```
As we can see, it is hard to read and maintain if we have to type a full-qualified class name in the annotation comment.

Second issue with the annotation approach is that it is unable to specify which method is expected to raise an exception exactly. As long as a matching exception is raised in the test method, PHPUnit will treat it as a successful test.

For example, in the code below, we expect a RuntimeException is raised in method divide(). But if RuntimeException is raised from the constructor (new Math()), PHPUnit will still consider it as a passing test.

```
/**
 * @expectedException \RuntimeException
 */
public function testDivide_runtime_exception_must_throw()
{
    $this->math = new Math();
    $this->math->divide(1,0);
}
```
The solution to the issues above will be a new method introduced in PHPUnit 5.2. It's expectException(). Just like using regular assert(), expectException() method expects the code below it to throw a specified exception. Since it is a class method, we can use the class constant when passing the expected exception class.

To solve the two issues mentioned earlier with expectException(), we do something similar to the code as shown below:

```
public function testDivide_runtime_exception_must_throw()
{
    $this->math = new Math();
 
    $this->expectException(CoreException::class);
 
    $this->math->divide(1,0);
}
```

## Protected/Private Method Testing
You do not test protected/private methods directly. Since anything non-public is only accessible within the scope of the class, we assume that your class’s public methods will interact with them, so in the end you are actually indirectly testing these methods anyway.

### Targeting private/protected methods directly
If we simply want to write more scenarios for a protected method or if we do not want to go through the public method directly, and instead want to interact solely with the protected/private method, you can access your instantiated objects’ privates rather easily:

```
<?php
/**
 * Call protected/private method of a class.
 *
 * @param object &$object    Instantiated object that we will run method on.
 * @param string $methodName Method name to call
 * @param array  $parameters Array of parameters to pass into method.
 *
 * @return mixed Method return.
 */
public function invokeMethod(&$object, $methodName, array $parameters = array())
{
    $reflection = new \ReflectionClass(get_class($object));
    $method = $reflection->getMethod($methodName);
    $method->setAccessible(true);

    return $method->invokeArgs($object, $parameters);
}
```
Using `invokeMethod()` you can easily call your private or protected methods directly without having to go through public methods.

To use, you simply do
```
<?php
$this->invokeMethod($object, 'methodName', ['parameter1']);
```
This would be the equivalent of simply typing
```
<?php
$object->methodName('parameter1');
```
assuming `::methodName()` was public.

## Code Coverage Analysis
PHPUnit’s code coverage analysis visualizes which lines of your code are being executed by your tests. It’s great to be able to see what lines of a method are untested so you can write a specific unit test to cover the scenario to execute that code, and sometimes catch a bug you didn’t know was there.

Code coverage with PHPUnit requires the Xdebug extension to be installed to get up and running.

The coverage report tool generates static HTML files for you to browse through and immediately see statistics of your codebase, including how much is covered by tests and how complicated your code is.

It will even tell you if you have missed any possible routes through our tested code, like not triggering an `if` statement and running the code inside the block, for example.

### Generating a coverage report
To generate, simply pass the --coverage-html flag to PHPUnit, along with the destination to have the files generated. For example:
```
cd tests
../vendor/bin/phpunit --coverage-html _output/coverage
```
If you look at your filesystem now, you will see that a `coverage` folder has been created and filled with HTML files. Open the `index.html` file.

## SonarQube
SonarQube is a code coverage tool that provides reasonable insight into the reports generated by your test suite and test cases.

We need coverage and test reports for our project to work with SonarQube. We need to i) execute your unit tests with test execution report in JUnit format and ii) generate a report in Clover XML format.

You can generate these coverage reports files by passing command line parameters to PHPUnit through the `–coverage-clover` flag and test log/report using `–log-junit` or by adjusting your PHPUnit configurtion file. In the php-pm, I added the following lines to the phpunit.xml file:

```
<logging>
   <log type="coverage-clover" target="_output/phpunit-coverage.xml"/>
   <log type="junit" target="_output/phpunit-report.xml"/>
</logging>
```
PHPUnit should now produce two files both will be in xml format which you need to reference in the sonar project properties file.

Next wherever these reports are generated we will need a `sonar-project.properties` file which will contain following elements:

```
sonar.projectKey=org.codehaus.sonar:php-sonar-runner-unit-tests
sonar.projectName=PHP project analyzed with the SonarQube Runner reusing PHPUnit reports
sonar.projectVersion=1.0<br/>
sonar.host.url=https://sonar.aws.sentry.investments
sonar.login=7fc4e289f9efef9cc970f8323fe27da824cd8e34
sonar.sources=src
sonar.tests=tests<br/>
sonar.language=php<br/>
sonar.sourceEncoding=UTF-8<br/>
# Reusing PHPUnit reports
sonar.php.coverage.reportPaths=tests/_output/phpunit-coverage.xml
sonar.php.tests.reportPath=tests/_output/phpunit-report.xml
```
All the attributes are easily understood and those which need explaining are:

* `sonar.sources=src`: This is the path of the actual PHP source code files for which the coverage is to be estimated.

* `sonar.tests=tests` : The directory where PHPUnit tests are located.

* `sonar.php.coverage.reportPaths=tests/_output/phpunit-coverage.xml`: Path where coverage reports generated through PHPUnit are located

* `sonar.php.tests.reportPath=tests/_output/phpunit-report.xml`: Path where logs generated through PHPUnit are located

The only step that remains is analyzing the project through SonarQube Runner. For that open up console and navigate to project that has `sonar-project.properties`, `tests` directory, `phpunit-report.xml` and `phpunit-coverage.xml`. Run command `npm run sonar` and it will analyze the project and show execution results on console. Now, to see the analysis results, go to https://sonar.aws.sentry.investments/projects and you will see you project appearing there.

## Test Double
One of PHPUnit's very powerful feature to help us handle outside dependencies is test double. It is very common in our code, a function of one class is calling another class's function. In this case, we have a dependency in these two classes. In particular, the caller class has a dependency on calling class. But as we already know, unit test should test the smallest unit of functionality, in this case, it should test only the caller function. To solve this problem, we can use test double to replace the calling class. Since a test double can be configured to return predefined results, we can focus on testing the caller function.

It basically involves replacing the actual object with a fake, or ‘mock’, object that we fully control, removing all dependencies on outside systems or code that we really have no need to test.

## PHPUnit’s Mock Helper
### Mock Object using getMockBuilder()
A mock object is an object that you would create using PHPUnit’s `getMockBuilder()` method. It is basically an object that extends the class you define and allows you to perform nifty tricks and assertions on it.
```
<?php
$calculator = $this->getMockBuilder('Calculator')
                   ->getMock();
```
`getMockBuilder()` returns a mock object, which is simply an object that has behavior similar to the original object. Your mocked object’s methods all return `NULL`.

These methods are considered stubs!

### Stub Method
A stub method is a method that mimics the origin method in two ways - same name and same parameters accepted. What makes a stub method special, however, is that all the code within it has been erased.

It is a method contained within a mock object that returns `null` by default, but allows you to easily override the return value.

All methods in your mock object are stubs, and they return `NULL`.

You can now override the value returned by a stub method from within your test.

This means that you define the value return by your stub in your test, and when you run your test your code will think the value returned is normal, and act accordingly to your wishes.

A returned value can be anything - `null`, a string, an array, integers, other objects and even other mocked objects.

## Overriding Stub Method Return Values
To override the return value of a stub, you have to be introduced to 5 new PHPUnit methods:
```
<?php
$calculator->expects($this->once())
           ->method('add')
           ->will($this->returnValue(6));
```
We are stating that the `$calculator` object expects to call one time the method `add()`, and it will return the value `6`.

You start this process off by calling `expects()`, which accepts a single parameter: the number of times we are expecting the method to be called in our code. There are multiple options for the number method, include `once()`, `any()`, `never()`, `exactly()` and a few more. The names are self-explanatory.

If we state that the method is expecting to be called one time, and it ends up never being called, or called more than once, our test will fail.

If we state it should never be called, but it is, the test will fail.

`any()` says, “I don’t care if it is ever called, but if it is, here is the expected return.“.

`method()` accepts the name of the method to override.

Then we have `will()` which is simply wraps the important `returnValue()` where you actually define what value is returned. In this case, it is `6`.

### Mock an object's properties
To add properties to a mocked object, you just set them as you'd normally do with an object:
```
$mock = $this->getMockBuilder('MyClass')
             ->disableOriginalConstructor()
             ->getMock();

$mock->property = 'some_value';
```
```
$mock->property will now return 'some_value'
```

### Mock Method
A mock method does the exact same thing its original method would. In other words any code that is in the method you are mocking will actually run and will not return `null` by default (unless that is what it originally did).

Basically mock methods are useful for when you want the code inside of it to run, but also want to do some assertions on the behavior of the method. These assertions could be that specific parameters are passed to the method (if it applies), or that the method is called exactly 3 times or not at all.

## Test with Test Double
It is meaningless to use test double to our calculator test case, since currently the Calculator class has no dependency on other classes. However to demonstrate how to use test double in PHPUnit, we will create a stub Calculator class and test it.

Let's add a test case called testWithStub to our existing class:
```
public function testWithStub()
{
    // Create a stub for the Calculator class.
    $calculator = $this->getMockBuilder('Calculator')
                       ->getMock();
 
    // Configure the stub.
    $calculator->expects($this->any())
               ->method('add')
               ->will($this->returnValue(6));
 
    $this->assertEquals(6, $calculator->add(100,100));
}
```
1. getMockBuilder() method creates a stub similar to our Calculator object.
1. getMock() method returns the object.
1. expects() method tells the stub to be called any number of times.
1. method() method specifies which method it will be called.
1. will() method configures the return value of the stub.

## The Four Pathways Of getMockBuilder()
There are actually 4 different ways of creating the object? It all depends on your use, or non-use, of the `setMethods()` method.

### Do not call setMethods()
```
<?php
$observer = $this->getMockBuilder(Observer::class)
                 ->getMock();
```
This produces a mock object where the methods
* Are all stubs,
* All return `null` by default,
* Are easily overridable

### Passing an empty array
You can pass an empty array to `setMethods()`:
```
<?php
$observer = $this->getMockBuilder(Observer::class)
                 ->setMethods([])
                 ->getMock();
```
This produces a mock object that is exactly the same as if you have not called `setMethods()` at all. The methods
* Are all stubs,
* All return `null` by default,
* Are easily overridable

### Passing null
```
<?php
$observer = $this->getMockBuilder(Observer::class)
                 ->setMethods(null)
                 ->getMock();
```
This produces a mock object where the methods
* Are all mocks,
* Run the actual code contained within the method when called,
* Do not allow you to override the return value

### Passing an array containing method names
```
<?php
$observer = $this->getMockBuilder(Observer::class)
                 ->setMethods(['insert', 'update'])
                 ->getMock();
```
This produces a mock object whose methods are a mix of the above three scenarios.

The methods you have identified
* Are all stubs,
* All return null by default,
* Are easily overridable

Methods you did not identify
* Are all mocks,
* Run the actual code contained within the method when called,
* Do not allow you to override the return value

This means that in the `$observer` mock object the `::insert()` and `::update()` methods would return `null` or you can override their return values, but any method within that class other than those two will run their original code.

## Why Would You Want Mock Methods?
Calling `exit` or `die` in your code will halt the current PHP execution, including any tests you may be running! That would be less than ideal.

The optimal solution would be not having `exit` or `die` in your code at all. You should always try to return a value instead. If you are unable to do this, another solution would be to wrap the `exit` or `die` in a method and stub it.

## References
1. [PHPUnit Beginner](https://www.startutorial.com/articles/view/phpunit-beginner-part-1-get-started)
1. [A short introduction to the test framework](https://pear.php.net/manual/en/package.php.phpunit.intro.php)
1. [Unit Testing Tutorial](https://jtreminio.com/blog/unit-testing-tutorial-part-i-introduction-to-phpunit/)
1. [Chapter 3. The Command-Line Test Runner](https://phpunit.de/manual/6.5/en/textui.html)
1. [Annotations](https://phpunit.readthedocs.io/en/7.4/annotations.html)
1. [PHPUnit assert that an exception was thrown?](https://stackoverflow.com/questions/5683592/phpunit-assert-that-an-exception-was-thrown)
1. [PHPUnit Mock an object's properties](https://stackoverflow.com/questions/44571002/phpunit-mock-an-objects-properties)
1. [Equivalent of SimpleTest “partial mocks” in PHPUnit?](https://stackoverflow.com/questions/1164192/equivalent-of-simpletest-partial-mocks-in-phpunit)
1. [SonarQube For PHP Project](https://www.sharpten.com/blog/2014/05/02/sonarqube_for_php_project.html)
1. [Chapter 11. Code Coverage Analysis](https://phpunit.de/manual/6.5/en/code-coverage-analysis.html)
1. [Continuous Code Quality Analysis with SonarQube](https://medium.com/@niko.huber/continuous-code-quality-analysis-with-sonarqube-6a9146912b7d)
1. [PHPUnit tips](https://www.startutorial.com/articles/view/phpunit-tips)

## Bibliography
1. [Unit testing with PHPUnit](https://www.youtube.com/playlist?list=PLfdtiltiRHWGXSggf05W-pJbD47-_d8bJ)
1. [How to use PHPUnit](https://www.youtube.com/watch?v=-9YVcssCACI)
1. [Chapter 04 Testing Your Application Part 04 Using Fixtures](https://www.youtube.com/watch?v=96o4DnBLUUM&index=78&list=PLxBrTCvVZHX8_cE3EELk2AKM8aZXqNn2H&t=138s)
1. [Getting Started with PHPUnit | Code Testing #3](https://www.youtube.com/watch?v=cRVkchE5kuE&index=83&t=0s&list=PLxBrTCvVZHX8_cE3EELk2AKM8aZXqNn2H)
1. [Hands-On Unit Testing With PHPUnit](https://www.youtube.com/watch?v=4BXpi7056RM&t=562s)
1. [Test Doubles](https://phpunit.readthedocs.io/en/7.4/test-doubles.html)
1. [PHPUnit Testing](https://www.youtube.com/playlist?list=PLe30vg_FG4OTsFRc1eWppZfYwZdMlLuhE)
1. [Unit testing with PHPUnit](https://www.youtube.com/playlist?list=PLFbnPuoQkKse0IoTKG8MZmjeu98DULDCe)
1. [PHP Unit Tests and Coverage Results Import](https://docs.sonarqube.org/display/PLUG/PHP+Unit+Tests+and+Coverage+Results+Import)
