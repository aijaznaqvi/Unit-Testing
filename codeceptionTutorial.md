# Automated Unit Testing with Codeception
## Unit Tests
A unit test verifies that a single unit of code is working as expected. That is, given different input parameters, the test verifies the class method returns expected results. Unit tests are usually developed by people who write the classes being tested.

Codeception supports all three testing types. Out of the box you have tools for writing unit, functional, and acceptance tests in a unified framework.

--  | Codeception Unit Tests | Codeception Functional Tests | Codeception Acceptance Tests
--- | --- | --- | ---
Scope of the test |	Single PHP class | PHP Framework (Routing, Controllers, etc.) |	Page in browser (Chrome, Firefox, or PhpBrowser)
Testing computer needs access to project’s PHP files | Yes | Yes | No
Webserver required | No | No | Yes
JavaScript | No | No | Yes
Additional software required | None | None | <ul><li>For WebDriver: Selenium Server or PhantomJS (deprecated)</li><li>For PhpBrowser: None</li></ul>
Test execution speed | High | High | Low
Configuration file | `unit.suite.yml` | `functional.suite.yml` | `acceptance.suite.yml`

One of the main advantages of Codeception is that you don’t have to decide on just one type of testing. You can have all three! That’s why Codeception consists of three so-called “suites”: A “unit suite” for all unit tests, a “functional suite” for all functional tests, and an “acceptance suite” for all acceptance tests.

Testing pieces of code before coupling them together is highly important as well. This way, you can be sure that some deeply hidden feature still works, even if it was not covered by functional or acceptance tests. This also shows care in producing stable and testable code.

## Codeception for Yii Framework
Codeception is created on top of `PHPUnit`. Codeception uses PHPUnit as a backend for running its tests. Thus, any PHPUnit test can be added to a Codeception test suite and then executed. Codeception adds some nice helpers to simplify common tasks.

Requirements and code can change rapidly, and unit tests should be updated every time to fit the requirements. The better you understand the testing scenario, the faster you can update it for new behavior.

Inside your `/tests` folder you will have three `.yml` config files and three directories with names corresponding to these suites: `unit`, `functional`, `acceptance`. Suites are independent groups of tests with a common purpose.

Unit tests are located in `tests/unit` directory and are supposed to contain all kind of unit and integration tests.

Each test case extends `Codeception\Test\Unit` class, which is standard Codeception format for unit testing. Tests are configured in `tests/unit.suite.yml` file with Yii2 module enabled:

```
modules:
       enabled:
         - Yii2:
               part: [orm, email]
```
This module starts Yii application for a test case and provides additional helper methods to simplify testing. It has only `orm` and `email` parts in order.

By accessing `$this->tester` class inside a test case you can use methods of Yii2 module. So if you have orm and email parts enabled so you can call methods belonging from these parts:

```
<?php
// insert records in database
$this->tester->haveRecord('app/model/User', ['username' => 'davert']);
// check records in database
$this->tester->seeRecord('app/model/User', ['username' => 'davert']);
// test email was sent
$this->tester->seeEmailIsSent();
// get a last sent emails
$this->tester->grabLastSentEmail();
```

If you enable fixtures part you will also get methods to load and use fixtures in your tests:

```
<?php
// load fixtures
$this->tester->haveFixtures([
    'user' => [
        'class' => UserFixture::className(),
        // fixture data located in tests/_data/user.php
        'dataFile' => codecept_data_dir() . 'user.php'
    ]
]);
// get first user from fixtures
$this->tester->grabFixture('user', 0);
```

## Configuration
Codeception has a global configuration in `codeception.yml` and a config for each suite.

## Running Tests
Tests can be started with the run command:
```
php vendor/bin/codecept run
```

With the first argument you can run all tests from one suite:
```
php vendor/bin/codecept run unit
```

To limit tests run to a single class, add a second argument. Provide a local path to the test class, from the suite directory:
```
php vendor/bin/codecept run unit SigninTest.php
```

Alternatively you can provide the full path to test file:
```
php vendor/bin/codecept run tests/unit/SigninTest.php
```

You can further filter which tests are run by appending a method name to the class, separated by a colon:
```
php vendor/bin/codecept run tests/unit/SigninTest.php:^testAnonymousLogin$
```

You can provide a directory path as well. This will execute all unit tests from the `models` dir:
```
php vendor/bin/codecept run tests/unit/models
```

Using regular expressions, you can even run many different test methods from the same directory or class. For example, this will execute all unit tests from the `models` dir beginning with the word “login”:
```
php vendor/bin/codecept run tests/unit/models:^login
```

### Reports
To generate JUnit XML output, you can provide the --xml option, and --html for HTML report.
```
php vendor/bin/codecept run --steps --xml --html
```
This command will run all tests for all suites, displaying the steps, and building HTML and XML reports. Reports will be stored in the  `tests/_output/` directory.

To see all the available options, run the following command:
```
php vendor/bin/codecept help run
```
### Generators
`generate:test` *suite* *filename* - Generates a sample PHPUnit Test with Codeception hooks

## Unit & Integration Tests
### Creating a Test
Create a test using `generate:test` command with a suite and test names as parameters:

```
php vendor/bin/codecept generate:test unit Example
```
It creates a new `ExampleTest` file located in the `tests/unit` directory.

As always, you can run the newly created test with this command:
```
php vendor/bin/codecept run unit ExampleTest
```
Or simply run the whole set of unit tests with:
```
php vendor/bin/codecept run unit
```
A test created by the `generate:test` command will look like this:
```
<?php

class ExampleTest extends \Codeception\Test\Unit
{
    /**
     * @var \UnitTester
     */
    protected $tester;

    protected function _before()
    {
    }

    protected function _after()
    {
    }

    // tests
    public function testMe()
    {

    }
}
```
Inside a class:
* all public methods with `test` prefix are tests
* `_before` method is executed before each test (like `setUp` in PHPUnit)
* `_after` method is executed after each test (like `tearDown` in PHPUnit)

Unit tests are focused around a single component of an application. All external dependencies for components should be replaced with test doubles.

A typical unit test may look like this:
```
<?php
class UserTest extends \Codeception\Test\Unit
{
    public function testValidation()
    {
        $user = new User();

        $user->setName(null);
        $this->assertFalse($user->validate(['username']));

        $user->setName('toolooooongnaaaaaaameeee');
        $this->assertFalse($user->validate(['username']));

        $user->setName('davert');
        $this->assertTrue($user->validate(['username']));
    }
}
```

### Assertions
There are pretty many assertions you can use inside tests. The most common are:
* $this->assertEquals()
* $this->assertContains()
* $this->assertFalse()
* $this->assertTrue()
* $this->assertNull()
* $this->assertEmpty()

Assertion methods come from PHPUnit.

#### expectException
Handles and checks exception called inside callback function. Either exception class name or exception instance should be provided.
```
<?php
$this->expectException(MyException::class, function() {
    $this->doSomethingBad();
});

$this->expectException(new MyException(), function() {
    $this->doSomethingBad();
});
```
If you want to check message or exception code, you can pass them with exception instance:
```
<?php
// will check that exception MyException is thrown with "Don't do bad things" message
$this->expectException(new MyException("Don't do bad things"), function() {
    $this->doSomethingBad();
});
```
* `param` $exception string or \Exception
* `param` $callback

### Test Doubles
Codeception provides Codeception\Stub library for building mocks and stubs for tests. Under the hood it used PHPUnit’s mock builder but with much simplified API.

#### Stubs
Stubs can be created with a static methods of `Codeception\Stub`.
```
<?php
$user = \Codeception\Stub::make('User', ['getName' => 'john']);
$name = $user->getName(); // 'john'
```

Inside unit tests (Codeception\Test\Unit) it is recommended to use alternative API:
```
<?php
// create a stub with find method replaced
$userRepository = $this->make(UserRepository::class, ['find' => new User]);
$userRepository->find(1); // => User

// create a dummy
$userRepository = $this->makeEmpty(UserRepository::class);

// create a stub with all methods replaced except one
$user = $this->makeEmptyExcept(User::class, 'validate');
$user->validate($data);

// create a stub by calling constructor and replacing a method
$user = $this->construct(User::class, ['name' => 'davert'], ['save' => false]);

// create a stub by calling constructor with empty methods
$user = $this->constructEmpty(User::class, ['name' => 'davert']);

// create a stub by calling constructor with empty methods
$user = $this->constructEmptyExcept(User::class, 'getName', ['name' => 'davert']);
$user->getName(); // => davert
$user->setName('jane'); // => this method is empty
```

Stubs can also be created using static methods from `Codeception\Stub` class. In this
```
<?php
\Codeception\Stub::make(UserRepository::class, ['find' => new User]);
```
#### Mocks
Declare mocks inside `Codeception\Test\Unit` class. To declare expectations for mocks use `Codeception\Stub\Expected` class:
```
<?php
// create a mock where $user->getName() should never be called
$user = $this->make('User', [
     'getName' => Expected::never(),
     'someMethod' => function() {}
]);
$user->someMethod();

// create a mock where $user->getName() should be called at least once
$user = $this->make('User', [
        'getName' => Expected::atLeastOnce('Davert')
    ]
);
$user->getName();
$userName = $user->getName();
$this->assertEquals('Davert', $userName);
```

##### public make($class, $params = null)
Instantiates a class without executing a constructor. Properties and methods can be set as a second parameter. Even protected and private properties can be set.
```
<?php
$this->make('User');
$this->make('User', ['name' => 'davert']);
?>
```
Accepts either name of class or object of that class
```
<?php
$this->make(new User, ['name' => 'davert']);
?>
```
To replace method provide it’s name as a key in second parameter and it’s return value or callback function as parameter
```
<?php
$this->make('User', ['save' => function () { return true; }]);
$this->make('User', ['save' => true]);
```
* `param mixed` $class - A class to be mocked
* `param array` $params - properties and methods to set
@return object - mock @throws \RuntimeException when class does not exist @throws \Exception

##### public makeEmpty($class, $params = null)
Instantiates class having all methods replaced with dummies. Constructor is not triggered. Properties and methods can be set as a second parameter. Even protected and private properties can be set.
```
<?php
$this->makeEmpty('User');
$this->makeEmpty('User', ['name' => 'davert']);
```
Accepts either name of class or object of that class
```
<?php
$this->makeEmpty(new User, ['name' => 'davert']);
```
To replace method provide it’s name as a key in second parameter and it’s return value or callback function as parameter
```
<?php
$this->makeEmpty('User', ['save' => function () { return true; }]);
$this->makeEmpty('User', ['save' => true));
```
* `param mixed` $class
* `param array` $params
* `param bool|\PHPUnit\Framework\TestCase` $testCase
@return object @throws \Exception

##### public makeEmptyExcept($class, $method, $params = null)
Instantiates class having all methods replaced with dummies except one. Constructor is not triggered. Properties and methods can be replaced. Even protected and private properties can be set.
```
<?php
$this->makeEmptyExcept('User', 'save');
$this->makeEmptyExcept('User', 'save', ['name' => 'davert']);
?>
```
Accepts either name of class or object of that class
```
<?php
* $this->makeEmptyExcept(new User, 'save');
?>
```
To replace method provide it’s name as a key in second parameter and it’s return value or callback function as parameter
```
<?php
$this->makeEmptyExcept('User', 'save', ['isValid' => function () { return true; }]);
$this->makeEmptyExcept('User', 'save', ['isValid' => true]);
```
* `param mixed` $class
* `param string` $method
* `param array` $params
@return object @throws \Exception

##### public construct($class, $constructorParams = null, $params = null)
Instantiates a class instance by running constructor. Parameters for constructor passed as second argument Properties and methods can be set in third argument. Even protected and private properties can be set.
```
<?php
$this->construct('User', ['autosave' => false]);
$this->construct('User', ['autosave' => false], ['name' => 'davert']);
?>
```
Accepts either name of class or object of that class
```
<?php
$this->construct(new User, ['autosave' => false), ['name' => 'davert']);
?>
```
To replace method provide it’s name as a key in third parameter and it’s return value or callback function as parameter
```
<?php
$this->construct('User', [], ['save' => function () { return true; }]);
$this->construct('User', [], ['save' => true]);
?>
```
* `param mixed` $class
* `param array` $constructorParams
* `param array` $params
* `param bool|\PHPUnit\Framework\TestCase` $testCase
@return object @throws \Exception

##### public constructEmpty($class, $constructorParams = null, $params = null)
Instantiates a class instance by running constructor with all methods replaced with dummies. Parameters for constructor passed as second argument Properties and methods can be set in third argument. Even protected and private properties can be set.
```
<?php
$this->constructEmpty('User', ['autosave' => false]);
$this->constructEmpty('User', ['autosave' => false), ['name' => 'davert']);
```
Accepts either name of class or object of that class
```
<?php
$this->constructEmpty(new User, ['autosave' => false], ['name' => 'davert']);
```
To replace method provide it’s name as a key in third parameter and it’s return value or callback function as parameter
```
<?php
$this->constructEmpty('User', array(), array('save' => function () { return true; }));
$this->constructEmpty('User', array(), array('save' => true));
```
To create a mock, pass current testcase name as last argument:
```
<?php
$this->constructEmpty('User', [], [
     'save' => \Codeception\Stub\Expected::once()
]);
```
* `param mixed` $class
* `param array` $constructorParams
* `param array` $params
@return object

##### public constructEmptyExcept($class, $method, $constructorParams = null, $params = null)
Instantiates a class instance by running constructor with all methods replaced with dummies, except one. Parameters for constructor passed as second argument Properties and methods can be set in third argument. Even protected and private properties can be set.
```
<?php
$this->constructEmptyExcept('User', 'save');
$this->constructEmptyExcept('User', 'save', ['autosave' => false], ['name' => 'davert']);
?>
```
Accepts either name of class or object of that class
```
<?php
$this->constructEmptyExcept(new User, 'save', ['autosave' => false], ['name' => 'davert']);
?>
```
To replace method provide it’s name as a key in third parameter and it’s return value or callback function as parameter
```
<?php
$this->constructEmptyExcept('User', 'save', [], ['save' => function () { return true; }]);
$this->constructEmptyExcept('User', 'save', [], ['save' => true]);
?>
```
* `param mixed` $class
* `param string` $method
* `param array` $constructorParams
* `param array` $params
@return object

##### public static never($params = null)
Checks if a method never has been invoked

If method invoked, it will immediately throw an exception.
```
<?php
use \Codeception\Stub\Expected;

$user = $this->make('User', [
     'getName' => Expected::never(),
     'someMethod' => function() {}
]);
$user->someMethod();
?>
```
* `param mixed` $params @return StubMarshaler

##### public static once($params = null)
Checks if a method has been invoked exactly one time.

If the number is less or greater it will later be checked in verify() and also throw an exception.
```
<?php
use \Codeception\Stub\Expected;

$user = $this->make(
    'User',
    array(
        'getName' => Expected::once('Davert'),
        'someMethod' => function() {}
    )
);
$userName = $user->getName();
$this->assertEquals('Davert', $userName);
?>
```
Alternatively, a function can be passed as parameter:
```
<?php
Expected::once(function() { return Faker::name(); });
```
* `param mixed` $params
@return StubMarshaler

##### public static atLeastOnce($params = null)
Checks if a method has been invoked at least one time.

If the number of invocations is 0 it will throw an exception in verify.
```
<?php
use \Codeception\Stub\Expected;

$user = $this->make(
    'User',
    array(
        'getName' => Expected::atLeastOnce('Davert')),
        'someMethod' => function() {}
    )
);
$user->getName();
$userName = $user->getName();
$this->assertEquals('Davert', $userName);
?>
```
Alternatively, a function can be passed as parameter:
```
<?php
Expected::atLeastOnce(function() { return Faker::name(); });
```
* `param mixed` $params
@return StubMarshaler

##### public static exactly($count, $params = null)
Checks if a method has been invoked a certain amount of times. If the number of invocations exceeds the value it will immediately throw an exception, If the number is less it will later be checked in verify() and also throw an exception.
```
<?php
use \Codeception\Stub;
use \Codeception\Stub\Expected;

$user = $this->make(
    'User',
    array(
        'getName' => Expected::exactly(3, 'Davert'),
        'someMethod' => function() {}
    )
);
$user->getName();
$user->getName();
$userName = $user->getName();
$this->assertEquals('Davert', $userName);
?>
```
Alternatively, a function can be passed as parameter:
```
<?php
Expected::exactly(function() { return Faker::name() });
```
* `param int` $count
* `param mixed` $params
@return StubMarshaler

## Integration Tests
Unlike unit tests integration tests doesn’t require the code to be executed in isolation. That allows us to use database and other components inside a tests. To improve the testing experience modules can be used as in functional testing.

### Using Modules
As in scenario-driven functional or acceptance tests you can access Actor class methods. If you write integration tests, it may be useful to include the `Db` module for database testing.
```
# Codeception Test Suite Configuration

# suite for unit (internal) tests.
actor: UnitTester
modules:
    enabled:
        - Asserts
        - Db
        - \Helper\Unit
```
To access UnitTester methods you can use the `UnitTester` property in a test.

### Testing Database
Let’s see how you can do some database testing:
```
<?php
function testSavingUser()
{
    $user = new User();
    $user->setName('Miles');
    $user->setSurname('Davis');
    $user->save();
    $this->assertEquals('Miles Davis', $user->getFullName());
    $this->tester->seeInDatabase('users', ['name' => 'Miles', 'surname' => 'Davis']);
}
```
To enable the database functionality in unit tests, make sure the `Db` module is included in the `unit.suite.yml` configuration file. The database will be cleaned and populated after each test, the same way it happens for acceptance and functional tests. If that’s not your required behavior, change the settings of the `Db` module for the current suite.

### Interacting with the Framework
You should probably not access your database directly if your project already uses ORM for database interactions. Why not use ORM directly inside your tests? Let’s try to write a test using Laravel’s ORM Eloquent. For this we need to configure the Laravel5 module. We won’t need its web interaction methods like `amOnPage` or `see`, so let’s enable only the ORM part of it:
```
actor: UnitTester
modules:
    enabled:
        - Asserts
        - Laravel5:
            part: ORM
        - \Helper\Unit
```
Let’s see how we can use the Laravel5 module for integration tests:
```
<?php
function testUserNameCanBeChanged()
{
    // create a user from framework, user will be deleted after the test
    $id = $this->tester->haveRecord('users', ['name' => 'miles']);
    // access model
    $user = User::find($id);
    $user->setName('bill');
    $user->save();
    $this->assertEquals('bill', $user->getName());
    // verify data was saved using framework methods
    $this->tester->seeRecord('users', ['name' => 'bill']);
    $this->tester->dontSeeRecord('users', ['name' => 'miles']);
}
```
In the example you should not be worried about the data persistence between tests. The Laravel5 modules will clean up the created data at the end of a test. This is done by wrapping each test in a transaction and rolling it back afterwards.

## DataProvider Annotations
You can also use the `@dataProvider` annotation for creating dynamic examples for Test classes, using a public method for providing example data. For more details about how to use data provider for unit tests, please refer to PHPUnit documentation.

## Code Coverage
At some point you want to review which parts of your application are tested well and which are not. Just for this case the CodeCoverage is used. When you execute your tests to collect coverage report, you will receive statistics of all classes, methods, and lines triggered by these tests. The ratio between all lines in script and all touched lines is a main coverage criterion. In the ideal world you should get 100% code coverage, but in reality 80% is really enough. Because even 100% code coverage rate doesn’t save you from fatal errors and crashes.

### Configuration
To enable code coverage put these lines in the global configuration file `codeception.yml`:
```
coverage:
    enabled: true
```
That’s ok for now. But what files should be present in final coverage report? Pass an array of files or directory to include/exclude sections. The path ending with ‘*’ matches the directory. Also you can use ‘*’ mask in a file name, i.e. `app/models/*Model.php` to match all models.

There is a shortcut if you don’t need that complex filters:
```
coverage:
    enabled: true
    include:
        - app/*
    exclude:
        - app/cache/*
```

The basic codecoverage can be collected for functional and unit tests. All you need is to execute codeception with --coverage option.

To generate a clover xml report or a tasty html report append also `--coverage-xml` and `--coverage-html` options.
```
codecept run --coverage --coverage-xml --coverage-html
```
XML and HTML reports are stored to the `_output` directory. The best way to review report is to open `index.html` from `tests/_output/coverage` in your browser. XML clover reports are used by Continuous Inspection products (like SonarQube).

#### SonarQube can't read Codeception report
The format of the report.xml file obtained after testing with phpunit differs from the report.xml file obtained after code execution.

Report sample after running phpunit:
```
<?xml version="1.0" encoding="UTF-8"?>
<testsuites>
  <testsuite name="" tests="75" assertions="164" failures="0" errors="0" time="25.978013">
    <testsuite name="Feature Tests" tests="60" assertions="149" failures="0" errors="0" time="24.027709">
      <testsuite name="Tests\Feature\AgeCategoryTest" file="/home/travis/build/tititoof/sas_judo/tests/Feature/Admin/AgeCategoryTest.php" tests="6" assertions="19" failures="0" errors="0" time="2.349255">
        <testcase name="testIndexStatus" class="Tests\Feature\AgeCategoryTest" file="/home/travis/build/tititoof/sas_judo/tests/Feature/Admin/AgeCategoryTest.php" line="40" assertions="2" time="0.416466"/>
        <testcase name="testStoreStatus" class="Tests\Feature\AgeCategoryTest" file="/home/travis/build/tititoof/sas_judo/tests/Feature/Admin/AgeCategoryTest.php" line="50" assertions="6" time="0.503893"/>
		...
      </testsuite>
      <testsuite name="Tests\Feature\AlbumTest" file="/home/travis/build/tititoof/sas_judo/tests/Feature/Admin/AlbumTest.php" tests="5" assertions="13" failures="0" errors="0" time="3.937930">
        <testcase name="testIndexAction" class="Tests\Feature\AlbumTest" file="/home/travis/build/tititoof/sas_judo/tests/Feature/Admin/AlbumTest.php" line="28" assertions="2" time="0.333698"/>
		...
      </testsuite>
	  ...
  </testsuite>
</testsuite>
```
Report sample after running codeception:
```
<?xml version="1.0" encoding="UTF-8"?>
<testsuites>
    <testsuite name="unit" tests="41" assertions="93" errors="0" failures="0" skipped="0" time="0.285144">
        <testcase file="/home/www/test/test/tests/unit/case/model/modelTagUnitCest.php" name="testGetById" class="modelTagUnitCest" assertions="2" time="0.045005"/>
        <testcase file="/home/www/test/test/tests/unit/case/model/modelTagUnitCest.php" name="testGetAll" class="modelTagUnitCest" assertions="1" time="0.001119"/>
        <testcase file="/home/www/test/test/tests/unit/case/model/modelTagUnitCest.php" name="testInsert" class="modelTagUnitCest" assertions="1" time="0.003246"/>
        <testcase file="/home/www/test/test/tests/unit/case/model/modelTagUnitCest.php" name="testUpdate" class="modelTagUnitCest" assertions="2" time="0.032069"/>
	<testcase file="/home/www/test/test/tests/unit/case/service/Reports/serviceReportsRunUnitCest.php" name="testUpdateReportVersion" class="serviceReportsRunUnitCest" assertions="2" time="0.085060"/>
        <testcase file="/home/www/test/test/tests/unit/case/service/Tags/serviceTagsCommonUnitCest.php" name="testGetAllTag" class="serviceTagsCommonUnitCest" assertions="2" time="0.017860"/>
        <testcase file="/home/www/test/test/tests/unit/case/service/Tags/serviceTagsCommonUnitCest.php" name="testUpdateTag" class="serviceTagsCommonUnitCest" assertions="2" time="0.001434"/>
        ...
    </testsuite>
</testsuites>
```
When executed with codeception, the entire result is output as a unit under suite unit.

This issue was fixed in Codeception 2.5.2, please upgrade and run Codeception with parameter `--phpunit-xml=report.xml` instead of `--xml`.

## References
1. [Unit Tests](https://www.yiiframework.com/doc/guide/2.0/en/test-unit)
1. [Introduction](https://codeception.com/docs/01-Introduction)
1. [Codeception for Yii framework](http://codeception.com/for/yii)
1. [Getting Started](https://codeception.com/docs/02-GettingStarted)
1. [Unit & Integration Tests](http://codeception.com/docs/05-UnitTests)
1. [Mocks](https://codeception.com/docs/reference/Mock)
1. [Asserts](https://codeception.com/docs/modules/Asserts)
1. [DataProvider Annotations](https://codeception.com/docs/07-AdvancedUsage)

## Bibliography
1. [Mastering Yii 2: Installing and Configuring the Test Components | packtpub.com](https://www.youtube.com/watch?v=5GkUG7TIbRU&list=PLxBrTCvVZHX8_cE3EELk2AKM8aZXqNn2H&index=77&t=435s)
1. [Codeception support in PhpStorm](https://www.youtube.com/watch?v=B3PE7w-jvjQ&list=PLxBrTCvVZHX8_cE3EELk2AKM8aZXqNn2H&index=79&t=18s)
1. [Yii2 Application Development Solutions - Vol 2 : Testing Application with Codeception | packtpub.com](https://www.youtube.com/watch?v=BPArclYhkE0&list=PLxBrTCvVZHX8_cE3EELk2AKM8aZXqNn2H&index=82&t=0s)
1. [Console Commands](https://codeception.com/docs/reference/Commands)
1. [Data Providers](https://phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.data-providers)
1. [Yii2 Lesson - 61 Fixtures](https://www.youtube.com/watch?v=SG5piu3ZrcA)
