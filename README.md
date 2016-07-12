# AI_3_TicketToRide
An AI agent to play board game Ticket to Ride

![alt tag](https://raw.githubusercontent.com/ss2cp/AI_HW3/master/ScreenShot.png)

## Background
This program uses BFS Search to find the shortest path between two cities, and Simulated Annealing to determine some of the actions to make. The simulator code was provided.

**Rules:** There are 2 types of cards: *destination cards* and *train cards*. There are 3 possible actions to take in each turn: *draw 2 desination cards*, *draw 1 train card*, or *buy 1 route*. There are multiple colors for train cards, including wild cards. Each route within the map has a color attribute and a cost attribute. Finishing destination cards will get a reward of the cost of that route, not finishing them will result in negative punishment of the reward. 

**Goal:** Within 200 turns, or before train cards run out, who has the higher points wins.

**Language:** Java

## Game Strategy
After a brief analysis of how human play this game, we found the following strategies:

1. **Finish game fast.** This gives us the advantage of having more routes purchased while our opponent is still building out his path or hoarding cards.
2. **Finish 2 destination tickets.** This gives a baseline for planning out routes, as well as takes away the negative penalty at the end of game due to incomplete destination tickets.
3. **Prioritize longer routes first.** Since the Value Per Train increases as the route becomes longer, the longer routes give a much better return on our investment and thus should be prioritized.
4. **If impossible to finish destination ticket, buy most expensive ones.** Normally, an average game with our algorithm will take up to 130 turns in total. So we implemented an annealing factor to deal with later in the game where the algorithm may not be able to finish the destination ticket.

##Pseudo Code for Decision Making
```java
annealingFactor(){
  if turn < 40
	  annealingFactor = (1 - (turn / 100)) * 80 + 20
  Else
	  annealingFactor = (1 - (turn / 100)) * 100
}

dicisionMake(){
  turn++ // increment turns counter
  TICKETS // set of all destination tickets in hand
  possibleRoutes // set of all AFFORDABLE routes sorted in descending cost order
  
  if TICKETS is empty
  	if turn <= 20
  		draw destination tickets // if finish all destination tickets before 20th turn, draw another 2
  	else
  		if currently have more than 5 train cards of same color
        claim affordable route
      else
        randomDecision // a random number < 100
        // more towards later the game, more likely to buy routes than drawing cards
        if randomDecision < annealingFactor
  		    drawTrainCard	// prefer rainbow cards
  		  else
  		    if possibleRoutes is not empty
  			    claim route
  		    else
  			    drawTrainCard // prefer rainbow cards
  else // still have destination tickets in hand
  	if there is an affordable route in shortest path of any of the destination tickets
  		claim route
  	else
  		drawTrainTicket // prefer rainbow cards
}
```

## Testing
We created 4 players for testing. We let MainPlayer play 50 games against the other 3 players. (25 where opponent went first)
####MainPlayer
MainPlayer implements the pseudo code given above.
####ShaoPlayer
ShaoPlayer implements a very similar algorithm as our main algorithm does, with the exception that ShaoPlayer does not use annealing.
####BasicPlayer
BasicPlayer’s strategy is to buy routes as soon as it can afford one. This strategy leads to a hoarding of short routes, which can potentially lead to blocking a path the opponent has planned.
####StupidPlayer
The baseline case is StupidPlayer. It only draws card and does nothing else. 

## Results
The results are as followed:

| Player 1  | Player 2 |P1 Average Score|P2 Average Score|Point Difference (P1-P2)|
| ------------- | ------------- | ------------- | ------------- |------------- |
| Main algorithm  | ShaoPlayer  |59.68|70.94|-11.26|
| Main algorithm  | BasicPlayer |82.85|76.29|6.56|
| Main algorithm  | StupidPlayer|90.94|0.00|90.94|


Inside src/ttr/model/player are the players created to play the game.


