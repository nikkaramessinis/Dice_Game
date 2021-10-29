-------------------------------------------------------------------------------------------------
                        		  Dice Game 
-------------------------------------------------------------------------------------------------

General:
We initialize an instance of DiceGame which keeps all the details of the game, both the 
controls and the view( cout commands in this case). Then MyAgent extends base class DiceGameAgent.
Initializing an instance of an agent we call function value_iteration which precalculates the best move for each 
state with states being thrown (1,1,1) ,(1,1,2)(1,1,3) .... (6,6,6) for a regular dice.  Then passing the instance of the game and the agent in the 
play_game_with_agent the agent plays the game for us. Since play_game_with_agent 
asks for the play of the Agent while the game hasn't finished, this returns  
play_game_with_agent 

Detailed Explanation of functions:
-My implementation

Agent is an autonomous entity which acts directing its activity towards achieving goals. MyAgent is the class which aims to take the best action for each state in this dice game.
MyAgent:

-> def __init__ 
	When the constructor is called we call the constructor of the base class causing it to get the access to the game variables and function.
	Initialize constants like discount_factor and theta, make a dictionary with a mapping from a state to the number where the state is.  And finaly call value_iteration.

-> def value_iteration(self):
	We initialize a best_values array with zeroes which will keep the best value of the best action for each state.
	While this is true we traverse each state then for each action we get all the possible next_states  whether the game is over or not the reward for this state and all the possible probabilities for the next states.
	As Bellman equation suggest we want the max value of the summation of the expected value of each action. With the expected value being the reward of the current plus the expected reward for all the next actions discounted by 
	some factor.
	If game_over asserts to true means the current_value (summation) is incremented by just the reward since there is no next action otherwise keep adding 
	v_cur += prob * (reward + self.discount_factor * best_values[self.dict[next_state]]) the expected reward for all of the actions. 
	If v_cur > best_values[s] means we have a new candidate for being the best_value for this state. So we keep the new v_cur as the new best expected_value  and the best given policy.
	At the  end of this we calculate the delta with the delta being the max difference from each states best value from the previous best value.
	if the delta is smaller than the our constant theta, then we trained our model enough. 

--> def play(self, state):
	play_game_with_agent provides us with the current state and we lookup in our best_policies and get the best action for the current state. 
	(This goes on and on inside play_game_with_agent until the game comes to an end)


-Existing classes/functions 
DiceGame:
-> def__init 
	Creates an array with values from 1 to sides incrementing by one if one does not exist already.
	Else checks if the length of values is equal to sides
	if there is no bias we just create a "fair" dice with bias 1/num of side else keep the bias array.
	Check that the size of values is the same with bias length.
	Created an array with the flip values e.g. 1/6 2/5 etc for a six sided dice.
	Create combinations of zero 1 and two dices (for loop) and extend it in the actions array.
	Create combinations with replacement for all values for the number of dices. e.g for four (1,1,1,1) is one etc.
Final score calls the final_score func for each state which calculates the final sum e.g for 1,1,2 6+6+2=12

-> reset(self)
	Reset some variables 
	game_over to false 
	score to penalty one if not set otherwise
	and gives a roll e.g random state

->final_score(self, dice)
	Takes current dice/tuple and gets the uniques and keeps the count for each on .
	then gets the ones which have more than one unique and flips them. Finally sums them all.

->def flip_duplicates(self):
	Gets the current_duce and flips the duplicates changing the current dice array.
def roll(self, hold=()):
	checks if hold is a valid action
	check if game over
	if number of hold is same as number of remaining dice that is final state, flip duplicates, count the score and return the relevant information.
	else check which ones we hold having an array(mask) with true/false which holds that information and roll the remaining by calling the random function with the relevant bias.
->def get_dice_state(self):
	returns the current_dices in a tuple
->def get_next_states(self, action, dice_state):
	Comments already exists with detailed explanation.
	
->def play_game_with_agent(agent, game, verbose=False):
Calls game.reset and while the game is not over calls agent.play increments actions and rolls the dices again.
When game is over returns the final score.


class DiceGameAgent:
inherits from ABC and in the constructor initializes self.game = game 
also has an abstract method which needs implementation from whoever extends this class.

class AlwaysHoldAgent(DiceGameAgent):
play function here returns always (0,1,2) which means always get all three dices at the first try whichever is the sum.

class PerfectionistAgent(DiceGameAgent):
play function here returns (0,1,2) if we 're in a state 1,1,1 or 1,1,6 which both give the perfect sum or doesn't take anything otherwise.


-Time Complexity

The time complexity for the each iteration of the value algorithm with S being all the states and A being all the actions is O(S^2*A) 
since we traverse all states and then for each action (all actions) we traverses all possible next states. This time complexity goes on 
until we converge. 


