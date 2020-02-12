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
  

## Refactoring
```py
class PhoneBookTest(unittest.TestCase): <-----Test SUITE
  def setUp(self) -> None:
  self.phonebook = PhoneBook()
  ```

## Vocabulary 
- Test Runner - **python -m unittest** (in command prompt)
- Test Case - 
- Test Suite- (Usually) will be made up of test cases from several different classes for different units

