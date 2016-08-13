The end result should be a little game that is modelled on a verbal game my wife and I sometimes play in long car rides. We both studied film directing and really like movies, and at some point we came up with Actor Domino (or, as we also call it "The IMDB game"). You can play it with at least 2 people and add as many players as you like. The first player names an Actress/Actor A that is the starting point. The next player now has to name another Actress or Actor B and a movie that starred those two actors. The next player now has to do the same with Actress/Actor B and a new Actor. The game is finished when you manage to come back to the initial Actor A.

Additional rules:
* Every actress/actor and every movie may only be referenced once in one game (exept the initial actor of course).
* When a player can't continue, he gets one minus point and the turn skipps to the next player. If no player can continue, the game is over and the player who mavuevered the game into the dead end is publicly shamed.
* Finishing the game by connecting back to the first actor earsn you two plus points

Ok, let's think about how that could be a computer game. A pretty straightforward translation could work like this:

* All players sit in front of the same computer
* The game begins by telling the game how many players are playing
* The first player enters the name of an actor into a search field
* To be able to verfiy if two actors played in the same movie, the game performs a XHR request to the universal movie database and searches for the actors name. This may come back with a list of potential matches.
* The player confirms the correct actor from the list
* The next player enters the name of the next actor, again choosing from a list if there are more than one match. The player also enters the title of the movie those two actors both starred in.
* The game confirms, with another search against the universal movie database, that this information is correct. If there are several movies with similar names that starr both actors, the user has to choose one.
* This continues for all players in turm, taking into account the additional rules and the game end conditions.
* The winner is the one who has the most points left

Puh, sounds like quite some complexity for such a small game. How should we tackle this? I think that a good way to go about these things is to think of some incremental steps that will get you to the final game. In this case, what is a very simple sub-problem that would already be useable on it's own? I think one thing that seems pretty obvious is entering an actors name, searching the universal movie database for that name, and presenting a list of all the matches, sorted by popularitiy. We can then add the logic to handle players and understand turns, recognizing when the game is finished, and finally checking the movie name the user enters and verifying that the two actors that are currently being matched really starred in that film.
