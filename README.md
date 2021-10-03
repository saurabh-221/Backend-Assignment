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

When a request comes, check whether it is present in the upRequest or downRequest arrays. If it is present then ignore the request else add a boolean value equal to true at the index equal to the floor number in the arrays.
Example: 
```sh
  upRequest[1] = true 
```

Then check for the nearest elevator and assign the request to the elevator. The nearest elevator is chosen by checking elevators

* without full capacity
* finding the difference of floors with current passenger floor
* if an elevator is idle or the elevator going in the same direction as the passenger.
* reassign an already assigned elevator if elevator is fully loaded.

```sh
  for each request in requests array {
     if(request.direction == up && upRequest[request.floorNumber] == true)
     {
         request already present
     }
     else if(request.direction == down && downRequest[request.floorNumber] == true)
     {
         request already present
     }
     else
     {
	assignElevator(Elevators, Request)
	   
	if(request.direction == up)
	{
	   upRequest[request.floorNumber] == true
	}
	else if(request.direction == down)
	{
	   downRequest[request.floorNumber] == true
	}
     }
  }
```

Assign an elevator to a passenger

```sh
  assignElevator(Elevators, Request) {
     appropriateElevator = findAppropriateElevator(Elevators, Request) 
     appropriateElevator.stops.add(request.floorNumber)
     if elevator.state == idle
        elevator.state = request.direction
     Request.assigned = elevatorId
     
     if(request.direction == up)
     {
	upRequest[request.floorNumber] == false
     }
     else if(request.direction == down)
     {
	downRequest[request.floorNumber] == false
     }
  }
```

Function to find the appropriate elevator based on elevator's current floor position, direction, and capacity

```sh
  findAppropriateElevator(Elevators, Request) {
     appropriateElevator = null
     minFloorDiff = INT.MAX
     for each elevator (
        if (fullCapacity == true)
	   continue;
	if (request.direction == elevator.state  || elevator.state == 'idle') {
	   if (elevator.state = up and elevator.floorNumber < request.floorNumber)
	      floorDiff = request.floorNumber - elevator.floorNumber
	   else if (elevator.state = down and request.floorNumber < elevator.floorNumber)
	      floorDiff = elevator.floorNumber - request.floorNumber
	   else 
	      wait till an elevator is idle or an elevator changes direction
	      
	   if (floorDiff < minFloorDiff) {
	      minFloorDiff = floorDiff
	      appropriateElevator = elevator
	   }
        } 
     }
     
     return appropriateElevator;
  }
```

All elevators are running in parallel.

Below function is called when elevator stops at a floor. We need to reassign a passenger request if elevator becomes full.

```sh
  updateElevatorState(Elevator) {
	remove floorNumber from stops array;
	if (stops array is empty)
		elevator.state = idle
	check elevator capacity is not full
		elevator.fullCapacity = false
	else
		elevator.fullCapacity = true
	if elevator.fullCapacity == true  {
		for each request with elevatorId == elevator.elevatorId {
			//reassign elevator
			assignElevator(Elevators, request)
		}
	}
  }
```

Function called when a passenger presses a button after entering the lift

```sh
  addFloorToElevator(Elevator, floorNumber) {
	Elevator.stops.add(floorNumber)
	check elevator capacity is full
		if full
			elevator.fullCapacity = true
	remove request from request array by searching requests array with the assigned elevator id
  }
```

For peek time. morning 8 - 9, evening 4 - 5 run below code

```sh
  for each elevators {	
	if (elevator.idle) {
		if  time between 7 to 8
			elevator.state = down until floor number is 0
		if time between 4 to 6
			elevator.state = up until floor number is top floor
	}
  }
```

Also at peek times assign elevators to some floors instead of assigning all elevators to all floors. floor numbers will be shown outside the lift.

i. e divide the floors into zones,  
 each zone will be provided elevators = elevators / number of zones

For example : say 200 floors and 100 elevators, so number of zones = 4, each zone will have 25 elevators.
