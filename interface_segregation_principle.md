Interface Segregation Principle
==============================

The Interface Segregation Principle states that “clients should not be
forced to depend upon interfaces that they don’t use”. In other words,
when a class implements an interface, it should not have to
include methods that it doesn’t need.  By ensuring that we adhere to
this principle, we can create code that is more easily refactored and
changed. The danger in using an interface that includes
methods that we don't need is that when other pieces of our software
that *do* need those methods require the methods to change, our
unrelated modules must also change. This will lead to modules that are unnecessarily coupled
and are very difficult to refactor, change and reuse.

To further understand this principle, let’s take a look at a Tic Tac Toe
game that allows the user to choose which mark they would like to use,
instead of making them be X every time.
```
defmodule Player do
  def choose_your_mark do
      #code that gets the user’s mark
  end

  def set_player_mark do
      #code that sets the player’s mark
  end

  def get_move do
      #code that gets the player’s next move
  end
end
```

If we are using this ```Player``` interface only for human players
this may be fine. But, it would make sense if we could also use this
interface for our computer player as well. A computer player module
that implements ```Player``` would need to include ```get_move``` and
```set_player_mark```, as expected. But, it would also have to include a
```choose_your_mark``` method as well. It makes sense that a ```ComputerPlayer```
may need to set their mark (depending on what the human chooses). And it is
pretty clear that a ```ComputerPlayer``` will need the ```get_move``` method so that
it can participate in the game! However, the computer will never
need to **choose** their own mark. This method would be entirely
wasted on a ```ComputerPlayer```, but since they are implementing
```Player``` it would be necessary.

As mentioned above, this scenario could result in some trouble. If the
```HumanPlayer``` ever needed to change the ```choose_your_mark```
method, the ```ComputerPlayer``` would also require some revisions, even
thought it never uses the method.

A way that we could fix this violation of the Interface Segregation
Principle would be to split up this ```Player``` interface into
separate, more specific interfaces.  The first interface could still be
```Player```, and would now only include ```set_player_mark``` and
```get_move```:
defmodule Player do
  def set_player_mark do
      #code to set the player’s mark
  end

  def get_move do
    #code to get the player’s move
  end
end

And then, we could bring the ```choose_you_mark``` into a separate module
which acts as the *user* interface.

Now, we are able to do something like this with our Player modules:

```
defmodule HumanPlayer do
  @user_interface UserInterface

  def set_player_mark do
    player_mark = @user_interface.get_player_mark
  end

  def get_move do
    #same as before
  end
end
```

```
defmodule ComputerPlayer do
   @human_player HumanPlayer

  def set_player_mark do
    player_mark = !@human_player.mark
  end

  def get_move do
    #this stays the same too
  end
end
```

Now, ```HumanPlayer``` and ```ComputerPlayer``` both have the same
interface with a ```GameSetup``` module, without any unused/unnecessary methods involved.
The implementation details of each ```set_player_move``` is very
different, but the ```GameSetup``` never needs to know. It will
always be able to call ```player.set_player_mark``` regardless of the
player type.


FINISh THIS LAST SENTENCE
An important thing to remember when implementing the Interface Segregation
Principle, is to create interfaces that are cohesive and decoupled. By
doing this, our interfaces tend to be specific to a certain client. In
Though at first this may seem like it limits the amount of code reuse,
An important part of this is to create cohesive, decoupled
interfaces, that are very specific. This then also allows for more code
reuse. If our interfaces are very specific, we can easily pick and
choose which ones are appropriate for our scenario.



