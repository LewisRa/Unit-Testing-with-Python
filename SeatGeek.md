# Seat Finder

Seat finder helps a theater choose seats available to be booked. 
The first test case is about deciding that we should choose seats closer to the front if they're available. 
So you construct your seat finder with a list or actually a set of the available seats, and then when you ask it to find one, 
it will give you the one nearest the front. 

The second test here is testing a different business rule. If you ask for two seats, then it will prefer to give you two that are next to
each other above giving you ones that are nearer the front. 

Then the third test here is another business rule. It's saying what to do when it can't find adjacent seats, that it should give you
the seats that are close to each other and to the front, presumably. 

 This fourth test here is for a negative case when there aren't suitable seats available. You've asked for five seats and there are only 
 three seats available, and it says that what you should do is return an empty set. Other valid options here might have been to throw an 
 exception. So clearly there's been some conversation between a business person who understands what rules should be applied, 
 and the developer who's written these tests, and these tests by themselves actually have become quite good documentation of what
 those decisions were that they made in that conversation.
 
 ```
 class SeatFinderTest(unittest.TestCase): 
 
  def test_ prefer_near_front(self):
    finder = SeatFinder(available_seats={"A6" , "B6", "C7"})
    seats = finder.find_seats(1)
    assert seats == {"A6"}
  
  def test_finds_adjacent_seats_when_more_than_one_requested(self):
    finder = SeatFinder(available_seats={"A6" , "A8", "C7", "C6"})
    seats = finder.find_seats(2)
    assert seats == {"C6", "C7"}
    
   def test_finds_separate_seats_when_adjacent_ not_available(self):
    finder = SeatFinder(available_seats={"A6" , "C7", "B6"})
    seats = finder.find_seats(2)
    assert seats == {"A6", "B6"}
    
    def test_finds_separate_seats_when_adjacent_ not_available(self):
      finder = SeatFinder(available_seats={"A6" , "C7", "B6"})
      seats = finder.find_seats(5)
      assert seats == {} ```
      
      ## Documenting the units
      
If you imagine you're a new developer coming to this code and you've been asked to add a new feature to find seats for 
a wheelchair user, you can look through these test cases and understand that the seat finder has a method find_seats, 
and you can decide that based on how it's being used here, the wheelchair user feature might be best served by adding 
an argument to that method that says how many of the seats to be found should be for wheelchair users. And you might write a 
test that looks like this: 
  
  ```def test_finds_seats_fro wheelchair_users(self):
        finder = SeatFinder(available_seats={"A1W", "A6" , "C7", "B6"})
        seats = finder.find_seats(1, wheelchair_count = 1)
        assert seats == {"A1W"} ```

      
      
Constructed a seat finder that has a wheelchair space available and make a design decision that it should be named W after the position of the seat.
Adding an extra argument to that says how many of the seats should be for wheelchairs and checking that it actually finds the seat that's suitable for a wheelchair user. 
