# Backend-Assignment
## Problem Statement

Consider we are building an algorithm for an elevator system for a
highrise building like Burj Khalifa. Typically these buildings would have a
considerable number of floors / stops. E.g. Burj Khalifa has 165 but let us
assume that the number can go upto 200.
Given the number of lifts are N, M people who are requesting lifts rapidly.
Design an algorithm which would solve these problems
- Minimise the wait time for each lift.
- In the mornings a lot of people would be going to their respective
offices [8am - 9am] so in the morning load of people going up
would be high
- In the evenings a lot of people would be going down so the load
will be high but it will be random [5pm - 9pm]
- Sometimes when a lift arrives it is full. We would like to avoid that
situation also.


## Solution

We can break down the problem into 3 systems -

* Request: Whenever a passenger presses an elevator button it becomes a request and it is added to requests array. Requests Array contains list of request objects.
  ```sh
  Request : {
	   id
	   floorNumber
	   direction -  (up or down)
	   time 
	   assigned : elevatorId
  }
  ```
  
  
* Schedular: Whenever a new request comes, it goes to the schedular. Schedular assigns an appropriate elevator to the particular floor.
  It consists of 2 arrays based on number of floors. 
  - upRequests
  - downRequests
  
  Example: If a up direction request comes from 2nd floor, then:
  ```sh
  upRequest[1] = true 
  ```


* Elevator: There is list of elevators. An Elevator object has following schema.
  ```sh
  Elevator: {
	   elevatorId
	   state : idle/ up/ down
	   fullCapacity : true / false
	   floorNumber
	   stops : [ ]     (floor numbers in sorted order)
  }
  ```
  Initialize all elevators as below:
  ```sh
  {
	   elevatorId = A10B1, A20C4, etc.  (unique identifier)
	   state = idle
	   fullCapacity = false
	   floorNumber = 0, 1, etc.
	   stops [ ]  - empty array
  }
  ```
  
## Approach

It can have 4 different states:

* Elevator is Ideal
* Elevator moving in same direction towards passenger and same direction passenger wants to go.
* Elevator moving in direction towards passenger but opposite to direction where passenger wants to go.
* Elevator moving away from passenger

#### As there are a large number of floors, lets say 200, and 100 elevators, we would have to use divide and conquer technique. We have to divide the whole system into zones and operate particular set of elevators on them.
#### We can even create zones and assign elevators to some floors instead of assigning all elevators to all floors in case of peak time that is between 8-9 AM and 5-9 PM. 

# Algorithm

