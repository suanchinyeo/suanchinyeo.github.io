---
layout: post
title: "How to: The Impossible TicTacToe Game"
author: "Suan Chin Yeo"
---

I built a [TicTacToe game](https://github.com/suanchinyeo/projects) for fun and thought I would write a simple how-to guide. 

If you're new to programming and haven't written a project of this size, it may seem very difficult to go from a blank script to a whole game. But if you already know a coding language, you can absolutely do it.

<video src="https://suanchinyeo.github.io/assets/tictactoe.mp4" width="638" height="354" controls preload></video>

Excuse the silent film and awkward pauses here and there. I tapped into my inner movie making genius and did not find much.

So let's start!

## The planning stage

We must always start with a plan. The more you plan, the less time you spend trying to make things work. For this stage, I like to write down anything that comes to mind on a piece of paper. I then identify key functionalities the code has to achieve and think of a way to best organize it. Sometimes, I do this while watching a tv show and would even go as far as to write the code on paper<sup id="a1">[1](#f1)</sup>. 

If you do this, you may end up with scribbles resembling this mess:
{% highlight markdown %}
game board
function that checks for winner/tie
players
players should have a character "x" or "o" they can play
an interface for the user
human player
computer player
an algorithm for computer player to play
Main game loop:
while game is not over:
	player1 plays
	if game is not over:
		player2 plays
if there is a winner:
	print Congratulations winner!
else:
	print Game Over! It's a tie!
Optional: ask if user would like to play another game.
{% endhighlight %}

But this is good! Now circle the things that can be grouped together, see if anything is redundant, highlight the key functionalities you need to write. And you may have an organization resembling this:

**class Player()**<br>
* Character (data)
* Name (data)

**class Human(Player)**<br>
* Play (method)

**class Computer(Player)**<br>
* Play (method)

**class Game()**<br>
* Board (data)
* Winner (data)
* isOver (method)
* getPlayers (method)
* player1 (data)
* player2 (data)

This is a skeleton to build our game on. We can go ahead and set up these classes and then start writing code for them in small sections. If you feel confused about what to include or how to implement an idea, just write down everything you think you have to set up for this section and go through the same process of organizing them.

## class Game()
### Board

What abstraction is best for the tictactoe board and pieces? In considering this problem, I happened upon [PyBites' TicTacToe challenge](https://pybit.es/codechallenge12.html). They suggested using the following to represent the board:

{% highlight markdown %}
1 2 3
4 5 6
7 8 9
{% endhighlight %}

And I thought it was a great idea to represent the board as a number pad! It's a well known pattern so the user would not have to learn a new system. It would be very convenient for me to use an abstraction that can easily map the user's input to update the `Game()`'s `Board`. For this reason, I chose to use the dictionary data structure. The keys will be numbers 1-9, representing the cells, and they will contain the contents of the cells. If we start the board with each cell assigned `False`, it may make our lives easier when we're trying to determine if a cell is empty later on. So, thus far we have:

{% highlight python %}
class Game():
	""" 
	Game should be able to do the following:
	0. Start game.
	1. Get players. 2 players are needed for each game, they can be human or computer.
	2. Determines if game is won.
	3. Determines winner.
	"""
	def __init__(self):
		"""
		Game has following objects:
		1. 3x3 board. This is represented in the following way:
				1 2 3
				4 5 6
				7 8 9 
		2. Players: player1 and player2.
		4. Winner: player1 or player 2 or None (tie/game is not yet over).
		"""
		self.board = {i:False for i in range(1, 10)}
		self.player1 = None
		self.player2 = None
{% endhighlight %}

This would be a good time to write a method for the `Game()` class that will allow us to print the board. Mine is nothing fancy, it literally is just the number pad as I showed above. But you can make this look very polished with boxes or the classic hash design if you have some talent with doing text-based art. But just the number pad by itself is very playable (I've tested it on people).

## Players

Let's set up our human players so we are able to test our code as we go along. You can do whatever you like as long as it works. You don't even have to do `o` and `x` for your pieces. Aim for an easy and simple interface. Test your code to make sure everything works!

Here's the structure I used:
{% highlight python %}
class Game():
	def __init__(self):
		self.player1 = None
		self.player2 = None
		self.getPlayers()
	def getPlayers(self):
		"""
		Prompts user to set player1 and 2 as either human or computer.
		Enter h or H or any string containing H to play as human.
		Leave blank for Computer.
		Creates the right type of player and assigns it to player 1 or 2.
		"""
class Player():
	def __init__(self, Game, n):
		"""
		Player has the following objects:
		Game: a Game object it is playing.
		Name: just for instructions and announcing winner.
		Char: this is the piece the player plays with. "x" or "o".
		Num: 1 or 2. Player1 or Player2 in self.game.
		"""
		self.game = Game
		self.Num = n
		self.name = None
		self.char = None
class Human(Player):
	def __init__(self, Game, n):
		Player.__init__(self, Game, n):
		self.name = self.setName()
		self.char = self.setChar()
	def setChar():
		"""
		If human player is player1, gets to choose between "o" or "x".
		If human player is player2, gets the leftover piece, and is informed of what
		that is.
		"""
	def setName():
		"""
		Ask for name.
		"""
class Computer(Player):
	"""
	Computer player. Like human player but makes best move available. Never loses.
	unlike human player, has self.opponent. So it is aware of its opponent's moves.
	"""
	def __init__(self, Game, n):
		Player.__init__(self, Game, n)
		if self.Num == 1:
			self.name = "player1"
		elif self.Num == 2:
			self.name = "player2"
		self.opponent = self.getOpponent()
		self.char = self.setChar()
	def setChar(self):
		""" 
		Due to lack of creativity, computer always picks "x" if given first choice.
		Else it will take leftover piece.
		"""
	def getOpponent(self):
		""" 
		Gets the opponent.
		If player1 is a computer and player2 has not been assigned, there's a line
		in Game()'s getPlayers() method that calls this method again for player1 after
		the player2 has been assigned.
		"""
{% endhighlight %}

We can now write a `play()` function for our `Human(Player)`. This again, can be done however you like as long as it works and is maintainable. 

I did it this way:
* Write an `updateBoard(location, character)` method for `Game()` that will update the `board` object by placing the character in the location specified. The advantage of doing this is that our `Computer(Player)` can use the same method to play.
* In `Human(Player)`'s `play()` method, it informs the player that it's their turn, and reminds them what their character is<sup id="a2">[2](#f2)</sup>. It then prints the current board and prompts the player to enter the number of the cell they would like to play. If it's not a valid move, it'll prompt for another number until it is valid. Once a valid number is obtained, it calls `self.game.updateBoard(input, self.char)`. That updates the board and the human has played!

## IsOver()

Forget about the `Computer(Player)` for now. Let's write the function that will determine if our game is over. Because this is such a simple game, the number of solutions to the game is very small, in fact, there are only 8 possible winning solutions:

{% highlight markdown %}
[1, 2, 3], [4, 5, 6], [7, 8, 9], [1, 4, 7], [2, 5, 8], [3, 6, 9], [1, 5, 9], [3, 5, 7]
{% endhighlight %}

For our game, let's consider a game over if:
* A player has 3 pieces in a line (the possible lines listed above)
* Neither player has a chance at winning. e.g. a line is considered a possible winning solution for player1 if player2 has not played in that line.

Thus, we just have to check each solution. If, for a solution, a player has filled all 3 positions with its piece, a winner is found and game is over. If both players have played in a single solution, that solution is no longer a possible winning solution for either players and can be removed from the list of possible solutions. By the time we finish, if there are no solutions left, game is over and it's a tie! While we're running this method after every move, we might as well assign a `solution` to the `Game()` class and update it so we don't do unnecessary calculations.

{% highlight python %}
class Game():
	def __init__(self):
		self.board = {i:False for i in range(1, 10)}
		self.player1 = None
		self.player2 = None
		self.solution = [[1, 2, 3], [4, 5, 6], [7, 8, 9], 
						 [1, 4, 7], [2, 5, 8], [3, 6, 9], 
						 [1, 5, 9], [3, 5, 7]]
		self.getPlayers()
		self.winner = None
	def isOver(self):
		"""
		If game is over and there is a winner, updates winner and returns True.
		If game is not there is no winner, returns True.
		If game is not over, updates solution by removing the solutions that are no longer
		possible for either player.
		"""
		solution = self.solution[:]
		board = self.getBoard()
		for i in solution[:]:
			p1 = 0
			p2 = 0
			for c in range(len(i)):
				if board[i[c]] == self.player1.getChar():
					p1 += 1
				if board[i[c]] == self.player2.getChar():
					p2 += 1
			if p1 == 3:
				self.winner = self.player1
				return True
			if p2 == 3:
				self.winner = self.player2
				return True
			if p1 != 0 and p2 != 0:
				solution.remove(i)
		if len(solution) == 0:
			return True
		else:
			self.solution = solution
{% endhighlight %}

Now that we have this, let's get back to our original goal. We can now write a `play()` method for our `Game()` object. Just as we had planned.

{% highlight python %}
def play(self):
		"""
		Player1 plays first, then player2.
		When game is over, announces winner or tie.
		"""
		while not self.isOver():
			self.player1.play()
			if not self.isOver():
				self.player2.play()
		if self.winner == None:
			print("Game Over! It's a tie.")
			print(self)
		else:
			print("Congratulations! "+self.winner.getName()+" has won!")
			print(self)
{% endhighlight %}

We can add this method to `Game()`'s `__init__()` method.

{% highlight python %}
class Game():
	def __init__(self):
		self.board = {i:False for i in range(1, 10)}
		self.player1 = None
		self.player2 = None
		self.solution = [[1, 2, 3], [4, 5, 6], [7, 8, 9], 
						 [1, 4, 7], [2, 5, 8], [3, 6, 9], 
						 [1, 5, 9], [3, 5, 7]]
		self.getPlayers()
		self.winner = None
		self.play()
{% endhighlight %}

Some quick notes on testing and troubleshooting. IDLE and Terminal are able to run python code that asks for user input. Some IDE's aren't, so do a quick check for yours before you waste too much time wondering why your code isn't working. If your IDE of choice doesn't take user input, you can code in your IDE of choice and test in Terminal. I prefer this to testing in IDLE, because IDLE opens the file again and then it's tempting to make changes to the file opened in IDLE instead of the one in the IDE ... it's just a whole mess you can avoid by testing in Terminal. 

To run your script in the terminal, type `Game()` at the bottom of the script. And open Terminal and navigate to the folder where your tictactoe.py is saved:
{% highlight markdown %}
$ cd <insert path for file here>
{% endhighlight %}
{% highlight markdown %}
$ python tictactoe.py
{% endhighlight %}

If you do test in Terminal, your user inputs must be quoted like a string: "H", for example. Otherwise it will not be read correctly. Inputs do not have to be quoted in IDLE.

At this stage you should be able to play human against human and the program should be able to detect a winner or a tie. I haven't showed all my code here, only explained what needs to be done for the program to work. So ensure you have all the working parts needed for your code. You can take a look at [my entire game](https://github.com/suanchinyeo/projects) which I also linked at the beginning of the article.

Once you've got everything working, congratulations! You have a working human vs. human TicTacToe game!

### Computer(Player)

Now, we are at our final step, which is to make a `Computer(Player)` that will win if given any opportunity to do so. This player makes this TicTacToe into an impossible game, because if played against the Computer, regardless of who plays first, only the Computer has a chance of winning.

There are a lot of algorithms available to write an unbeatable TicTacToe player. However, you don't actually need them because you _are_ an unbeatable TicTacToe player yourself! So, you already an algorithm for being unbeatable, now all we have to do is to write that down for our `Computer(Player)`.

{% highlight python %}
def play(self):
		"""
		Computer plays by following these instructions:
		1. If computer has a possible win within 1 move, it will make that move.
		2. If 1. is not true, and opponent has possible win within 1 move, it will block that win.
		3. If neither 1 nor 2 is true, computer will look at its possible solutions (the solutions
		in which its opponents have yet to play), and pick the cell that appears in the most
		possible solutions.
		4. If none of the above is true, it will pick a random empty cell.
		"""
{% endhighlight %} 

Think about how this can be implemented or if you have a better winning strategy. The idea is very similar to how we determine if the game `isOver()`. We just search through the solutions and if Computer has a chance at winning (its opponent hasn't played in this solution), we count how many pieces Computer has played. If it is 2, play the remaining piece. If not, we can record this cell number and add 1 to its score as it contributes to a possible winning solution. Then we look at the solutions where the opponent has a chance at winning (Computer has not played in these solutions). If the opponent has played 2 pieces in a solution, we play the remaining cell to block a win. Otherwise, we go back to the record of cells and their scores and play the cell with the highest score. If every cell scored 0, we just pick a random empty cell.

Test this code by playing it several times with several different moves. Does the computer know to win if you let it win? Does it always thwart you? Does it throw up error messages when it no longer has good moves?

And we're done!

<center>Footnotes</center>

 <b id="f1">1</b> The same principle applies to writing essays, if you have a 10 page essay assignment, spend time thinking about what you would like to write while doing everyday tasks. That way, by the time you sit down in front of a computer, you have a fairly good idea of how it should be organized and will write much faster.[↩](#a1)

 <b id="f2">2</b> You will find this is necessary after a few test runs, you won't be able to remember whether you chose `x` or `o`![↩](#a2)
