# Unit Testing with-Python

Unit testing doesn't not use the Filesystem, a database, or the network. You want your tests to run entirely in memory where they can be very much simpler to code, and they can run extremely fast and still prove something very useful about your code. 

 By convention, it's named test_ and then the name of the thing that you're testing

## Example 1: Phonebook case

Given you have a list of names and phone numbers, make a phone book. That is, a catalog where you can look up somebody's phone number if you know their name. 

- Determine whether the phonebook is consistent.
  -A list of phone numbers is consistent if no number is a prefix of another number. (e.g. If Bob's number is 91125426, Anna's number is 97625992, and Gayle's number is 91156328, the unit test will throw an error because of Bob's and Gayle's numbers are inconsistent

assertEqual  

- Check what happens when I look up a name that is not present in my phonebook. 
  - Test raise a key error (test_missing_name).  I need to put it in a context manager so that unittest will ensure that the lines enclosed with the with statement will throw a key error.  So let's run our new test case. As we can see, it passes straightaway. I'll just check that it did run the new test, and here you can see it did. The reason it passes is because we're using a dictionary underneath in the phonebook, and that's exactly the behavior of the dictionary. 
  
 - Check all the names and all the numbers in my phonebook
  - This test makes sure the add works properly so it's checking that .get_names() and get_numbers() can retrieve all namesa and numbers in phonebook. We need two assertions in this test case, which is a little unusual for a unit test, but in this case, both the asserts are referring to the behavior of the same thing, the add method. So this is a situation where you might want two asserts. 

 ```
 def test_phonebook_adds_names_and_numbers(self):
  self.phonebook.add("Sue", "123343")
  self.assertIn("Sue", self.phonebook.get_names())
  self.assertIn("123343", self.phonebook.get_numbers())
  ```
  
#### To skip a test while running the test suite: 
```py
@unittest.skip("WIP")
 def test_empty_phonebook_is_consistent(self):
  phonebook = PhoneBook()
  self.assertTrue(phonebook.is_consistent())
  ```
## Refactoring
```py
class PhoneBookTest(unittest.TestCase): <-----Test SUITE
  
  def test_lookup_by_name(self):
   self.phonebook = PhoneBook()
   phonebook.add ("Bob", "12345")
   number = phonebook.lookup("Bob")
   self.assertEqual("12345, number
   
  def test_missing_name(self):
   phonebook= PhoneBook()
   with self.assertRAises(KeyError):
    phonebook.lookup("missing")
    
  def test_empty_phonebook_is_consistent(self):
   phonebook = PhoneBook()
   self.assertTrue(phonebook.is_consistent())
   
  ```
 **With Refactoring**:
```py
class PhoneBookTest(unittest.TestCase): <-----Test SUITE
  
  def setUp(self) -> None:
   self.phonebook = PhoneBook()
   
  def tearDown(self) -> None: 
   pass
   
  def test_lookup_by_name(self):
  phonebook.add ("Bob", "12345")
  number = phonebook.lookup("Bob")
  self.assertEqual("12345, number

  def test_missing_name(self):
   with self.assertRAises(KeyError):
    phonebook.lookup("missing")
    
  def test_empty_phonebook_is_consistent(self):
   self.assertTrue(phonebook.is_consistent())
  ```
tearDown  gives you the opportunity to release any resources that you may have reserved in the setUp method or during the test. In this small case, phonebook is entirely in memory so there's nothing actually needed to release. In a more general case though, if you're, for example, creating files or setting up a database connection, you might need to use the tearDown method to release those resources.

## Vocabulary 
- Test Runner - **python -m unittest** (in command prompt)
- Test Case - 
- Test Suite- (Usually) will be made up of test cases from several different classes for different units

## Test Case Design: 
- Test name design is very important
- Arrange-Act-Assert

## What is unit testing for: 
- Understand what you build
- Design the units
- Document the units
- Regression protection

## Test Doubles

- Dummy Object 
- Test Stub
- Test Spy
- Mock Object 
- Fake Object
- Mock and spies are very similar. Unlike stubs, they make assertions about what hapened in the test case. Mock and spies will cause a test to fail while a stub will not. 

3 kinds of Assert: 1) Return value or exception 2) State change - use a API to quer the new state 3) Method call

*We can replace a File with StringIO. In a similar way, you can replace a normal database with a fast in-memory database, or a real web server with a lightweight web server. *

# PyTest

## @pytest.fixture(scope="module")
```py
# content of conftest.py
import pytest
import smtplib


@pytest.fixture(scope="module")
def smtp_connection():
    return smtplib.SMTP("smtp.gmail.com", 587, timeout=5)
```

```py
# content of test_module.py

def test_ehlo(smtp_connection):
    response, msg = smtp_connection.ehlo()
    assert response == 250
    assert b"smtp.gmail.com" in msg
    assert 0  # for demo purposes


def test_noop(smtp_connection):
    response, msg = smtp_connection.noop()
    assert response == 250
    assert 0  # for demo purposes
```


```diff
$ pytest test_module.py
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y
cachedir: $PYTHON_PREFIX/.pytest_cache
rootdir: $REGENDOC_TMPDIR
collected 2 items

test_module.py FF                                                    [100%]

================================= FAILURES =================================
________________________________ test_ehlo _________________________________

- smtp_connection = <smtplib.SMTP object at 0xdeadbeef>

    def test_ehlo(smtp_connection):
        response, msg = smtp_connection.ehlo()
        assert response == 250
        assert b"smtp.gmail.com" in msg
>       assert 0  # for demo purposes
E       assert 0

test_module.py:7: AssertionError
________________________________ test_noop _________________________________

- smtp_connection = <smtplib.SMTP object at 0xdeadbeef>

    def test_noop(smtp_connection):
        response, msg = smtp_connection.noop()
        assert response == 250
>       assert 0  # for demo purposes
E       assert 0

test_module.py:13: AssertionError
========================= short test summary info ==========================
FAILED test_module.py::test_ehlo - assert 0
FAILED test_module.py::test_noop - assert 0
============================ 2 failed in 0.12s =============================
```

**You can  see that the same (module-scoped) smtp_connection object was passed into the ---two--- test functions because pytest shows the incoming argument values in the traceback. As a result, the two test functions using smtp_connection run as quick as a single one because they reuse the same instance.**

### @pytest.fixture(scope="session") - the returned fixture value will be shared for all tests needing it

### @pytest.fixture(scope="class") scope will invoke the fixture once per test class.

## tmpdir

You can use the tmpdir fixture which will provide a temporary directory unique to the test invocation, created in the base temporary directory.

## yield

![](https://github.com/LewisRa/Unit-Testing-with-Python/blob/master/markdowmImages/yield.PNG)

pytest runs the fixtures code as usualuntil it gets to the yield statement. Pytest then runs all test associated with this particular fixture. The test functions goes ahead and runs all the way through and finishes and then the code after the yield statement is executed which in this case cleans out the database. This is a really clean approach as we can define what we need to do to set up a fixture alongside what we need to do to tear it down and clean it up afterwards and the yield statement acts as the
divider between setup and teardown

NOTE: With Yield, even if the test fails or something goes wrong in our test function that stops the test from finishing
we still run that teardown code
![](https://github.com/LewisRa/Unit-Testing-with-Python/blob/master/markdowmImages/yield2.PNG)

## pytest -m (Working with custom markers)

You can “mark” a test function with custom metadata like this:
```

import pytest


@pytest.mark.webtest
def test_send_http():
    pass  # perform some webtest test for your app


def test_something_quick():
    pass


def test_another():
    pass


class TestClass:
    def test_method(self):
        pass
```
You can then restrict a test run to only run tests marked with webtest:
```diff
> $ pytest -v -m webtest
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y -- $PYTHON_PREFIX/bin/python
cachedir: $PYTHON_PREFIX/.pytest_cache
rootdir: $REGENDOC_TMPDIR
collecting ... collected 4 items / 3 deselected / 1 selected

test_server.py::test_send_http PASSED                                [100%]

===================== 1 passed, 3 deselected in 0.12s ======================
```
Or the inverse, running all tests except the webtest ones:

```diff
> $ pytest -v -m "not webtest"
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y -- $PYTHON_PREFIX/bin/python
cachedir: $PYTHON_PREFIX/.pytest_cache
rootdir: $REGENDOC_TMPDIR
collecting ... collected 4 items / 1 deselected / 3 selected

test_server.py::test_something_quick PASSED                          [ 33%]
test_server.py::test_another PASSED                                  [ 66%]
test_server.py::TestClass::test_method PASSED                        [100%]

===================== 3 passed, 1 deselected in 0.12s ======================
```


**https://docs.pytest.org/en/latest/example/markers.html**

##  pytest -s            #  -s   Show Output, do not caputure. During test execution any output sent to stdout and stderr is captured. If a test or a setup method fails its according captured output will usually be shown along with the failure traceback. 
## pytest -vv            # Run tests in verbose mode(output with less words), useful for finding small differences in assertions. If you want your output to be less verbose, try pytest -q or pytest --quiet

**https://mattscodecave.com/posts/pytest-cheatsheet.html**
