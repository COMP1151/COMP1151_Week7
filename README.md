# Week 7 -- State Machines --

Learning outcomes:

* How to set up a simple State Machine.
* How to make state machines transitions based on events or conditions.

## Foreword

Today we are looking at how to implement a somewhat complex AI using something known as a "state machine". Visually this is quite straight forward, and a lot of you will quickly understand how this concept might exist in your favourite games (action RPG's are probably a good example).

The number of tasks today is smaller than the previous ones, but all of the tasks have a lot of steps required (with more critical thinking required/less assistance given), so please take your time and read the instructions carefully.

## What is a State Machine

Simply put, a "**State Machine**" (in computer science) is a behavioural design pattern that lets an object alter a behaviour when its internal "**state**" changes, as if it has changed script entirely. We can swap from one state to another with some set rules or checks, called "**transitions**". Both states and transitions are finite and predetermined.

This is just a brief explanation, but it is recommended you do your own research on the topic (unfortunately this  will not be covered in lectures). Just note that "***Finite*** **State Machines**" and "**State Machines**" overlap with similar concepts-- when we talk about State Machines in Unity, we're actually referring to a 'Finite' State Machine

### How do we use State machines in Unity

In Unity, this essentially lets us swap from one logic routine to another, based on our transition, basically the equivalent from having our player set up, and after a transition, swapping to a completely different player script. That is essentially what we will be doing today, but we're instead going to make a new obstacle for our player.

## Setting up

Like last week, we're going to be working in a new repository & it's assumed you've completed all of the major tasks from last week:

* Implemented trigger interactions (using **Colliders** and **Rigidbodies**)
* Implemented (moving) asteroids
* Implemented a health system & health pickups
* Used the Layer Collision Matrix

Like previously, if you have not completed these tasks, go back and complete them before continuing on with this prac.

Otherwise, export your previous weeks work into a package, and import it into this weeks repository. Once imported, verify that everything is there and functioning correctly.

Note: **You will likely need to create your Layers again, as well as redo your Layer Collision Matrix**. You will also likely need to regenerate your visual scripting nodes (`Edit > Project Settings`, navigate to the `Visual Scripting` tab, and then press the `Regenerate Nodes`).

## Creating a state machine

* Before we get started, we'll need to add a new enemy sprite. We would recommend one of the UFO sprites (found in the PNG folder of the KennyNL sprites package). Note that if you did the optional challenge a few weeks ago (where you made a chase AI), you will want this to be visually distinct (different colour & shape), and you may also want to delete the Chase enemy a prefab and remove it from the scene, just to reduce clutter.

Once you've added your UFO enemy give it a **Rigidbody** and **Collider** (2D) , similar to the asteroids from last week. We're functionally going to make it act similar, where if it collides with the player, the player will take damage. After you/ve added a Rigidbody and a Collider, you will also need to add a **State Machine** component. It should look like this:

<img src="PracResources\images\00_StateMachineComponent.png">

Once you've done that, navigate to your `VisualScripts` folder in the Project in your **Project** panel, and Right-click in an empty space, and find `Create > Visual Scripting > State Graph`. As always, you can instead choose to do this by selecting the top bar in Unity, and selecting `Assets > Create > Visual Scripting > State Graph`. Give it a sensible name (like "`PatrolEnemy`"), and then attach the graph to the State Machine component on the enemy UFO:

<img src="PracResources\images\01_FilledStateMachineComponent.png">

Once you've done that, open up your visual script. State Graphs function slightly different to Script Graphs, as there is no search bar needed, instead all of the nodes & interactions you will need are simply found by Right-Clicking.

Start off by right clicking in an empty space in the blackboard, and selecting `Create Script State`. This will automatically be called "`Start`" by default, and you will notice it has some parameters inside it

<img src="PracResources\images\03_StartStateNode.png">

These parameters actually preview what event nodes exist inside the state itself, which you can see (or edit) by double clicking the state itself. Note that to exit the inside of your state (and look back at the overall state graph), click the State Graph icon (or the text beside it) in the top-left corner of the visual graph editor.

<img src="PracResources\images\04_GoBackStateGraph.png">


|   |                                                                                                                                       Note                                                                                                                                       |   |
| --- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | Associated text besides this icon changes depending if you are editing the Graph from the asset itself (in the**Project** view), or the game object itself, for whatever reason. Don't worry if the name of the state graph shows the name of the UFO instead of your Graph name |   |
|   |                                                                                                                                                                                                                                                                                 |   |

To start with, we're going to simply transition out of the `Start` state, and into a "`Chase`" state, one that will pursue our player. Before continuing, give our UFO the following variables :

* A variable that contains the player Transform
* A variable (float) to act as our move speed - make it 0.25 for now

<img src="PracResources\images\05_UFOInitialVariables.png">

Once you've added those variables, in your state graph select `Create Script State` like before. Select it, and in the **Graph Inspector**(top-left) of your window, change the title of this State to "`Chase`".

<img src="PracResources\images\06_LabelChase.png">

And finally, connect these two states by selecting the `Start` state, and right-click and select `Make Transition`. This will make an input pipe appear; connect it from `Start` to chase like so:

<img src="PracResources\images\07_InitialTransition.png">

You can label this transition node as well if you'd like (the same method as labelling `Chase`).

To make `Start` actually transition from itself to `Chase` though, we need to give the transition a trigger. For now we'll keep it simple; double click the transition (just like entering a state), and you'll see a `Trigger Transition` node. Simply add an `On Start` node, and connect it to the trigger transition.

<img src="PracResources\images\08_FirstTransOnStart.png">


|   |                                                                                                                                                                                                              Why bother adding Start                                                                                                                                                                                                              |   |
| --- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | While it might seem a bit wasteful to make an empty state that we're transitioning out of, sometimes we will need to run initialisation operations within a Start state, before entering our other states, therefore it is generally good practice to include a`Start` state. However, once you're a bit more proficient with state machines, it is reasonable to simply drop an empty `Start` state when making small scale/simple state machines |   |
|   |                                                                                                                                                                                                                                                                                                                                                                                                                                                   |   |

### Chase State

There are several ways to go about having our UFO enemy chase our player. Generally speaking, you'd need obtain a direction or a heading from your current position to your target, and then figure out how often you want to move. Note that the approach from last week (for the asteroids) won't quite work, since our direction needs to be constantly recalculated.

We'll be going over a relatively simple and intuitive method for this, but if you would like to learn about other methods, you may want to look into **Lerp**, and **InverseTransformDirection** - part of our optional challenges for today. Additionally, we do not recommend you use velocity/physics for this

For now though, we'll simply be using the `MoveTowards` node, which does exactly what it implies by its names and inputs.

Navigate to the inside of your `Chase` state, and create a `Transform: Set Position` node that connects to the already existing `On Update` event node.

Once you've done that, make a `MoveTowards: (in Math/Vector 2)` node.

<img src="PracResources\images\10_MoveTowardsSearch.png">

Once that has been created, click the `Per Second` toggle within the node. The node should look like this.

image - MoveTowardsNode
<img src="PracResources\images\11_MoveTowardsNode.png">

Have a go at setting this up yourself, considering the above logic that has been laid out. Don't overthink this, you will be using the variables you have already made (Hint: MaxDelta will require our `moveSpeed`) -- as always, you can find a solution further below. Note, if you cannot see your variables, read the breakout box below.


|   |                                                                           Heads up                                                                           |   |
| --- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------: | --- |
|   | If you cannot see the Object variables you made for your enemy, make sure you've selected the UFO object (in the**Hierarchy**) and are editing it from there. |   |
|   |                                                                                                                                                               |   |

Once you have finished this, you may wish to "print" the result of your `MoveTowards` (using a `Debug Log` node). Keep in mind that if you wish to do this, you will probably want to remove the debug log node from your player, to reduce clutter in your console.

Check that this works, you may wish to inspect your graph

After you've verified it is working, you will need to make new functionality in the Player graph to make it damage the player. We could make it instantly destroy the player, but it would be more interesting if it instead damages the player while the triggers are touching -- perfect for an `On Trigger Stay 2D` node.

Create a new layer for the UFO, then using same method you made last week, in your player script check if the player is touching the new UFO layer (using `On Trigger Stay 2D` event node). It would make sense if you then enter in the "Take Damage" logic from your asteroid as well, similar to the following

<img src="PracResources\images\12_TriggerStayLogic.png">

Verify this works as intended.

Before moving on, ensure your UFO Chase state looks similar to this (with or without the `Debug Log` node)

<img src="PracResources\images\13_ChaseInitialComplete.png">

### Dormant State & Complex Transitions

We've got something working, but at this point in time we're not really tapping in to what makes State machines interesting and powerful. Let's make some more interesting transitions to move in and out of our chase state. For now, lets keep things simple and make it transition from an "idle" state.

Create a new state called `Dormant`, and make the initial transition from the `Start` state instead transition to our new `Dormant` state (using the same set up as before, with the `On Start` node). Then, make a transition from both `Dormant > Chase`, and `Chase > Dormant`.

Once done, your State Graph should look like this:

<img src="PracResources\images\20_DormantStateIncluded.png">

As mentioned before, this time we're simply leaving our `Dormant` state empty. Instead, open up the transition from `Dormant > Chase`.

Consider our problem:

```text
We want to check (on Update), if the distance from the UFO and the player is less or equal to a given radius (i.e. the player is inside or touching the radius). If it is inside the radius, we want to then trigger the transition to the chase state.

```

Think about this for a moment, then implement a solution -- as always you can find the solution further below. Here are some tips:

* You should create a variable for the range of the UFO detection radius (e.g. "`DetectionRange"` make sure it is a float)
* You should use the Vector2 Distance node
* You already have the **Transform** of the player, but you will need to specifically get the position

Think about this, then have a go at making your solution. If you get stuck, you may wish to use Debug Logs as you go (e.g. to check if the player is actually in the radius)

Once you've implemented this, implement the opposite -- edit the transition for `Chase > Dormant`, but instead check if the player is outside the detection radius (i.e. the distance is greater than the detection radius)

If you get stuck, the below solutions may be able to help:

<img src="PracResources\images\21_DormantToChase.png">

<img src="PracResources\images\22_ChaseToDormant.png">

## Expanding our State Machine

While it's fine to have our UFO simply be dormant while it isn't directly interacting with the player, this is quite boring. We've had our player move towards one point, the player, but now let's have it move between two points when it isn't chasing the player.

Make your UFO object a child of a new empty game object, it can be called something simple like "`UFO_Parent`". Once you've done that, make two new empty game objects, called `PatrolPointA` and `PatrolPointB` respectively. In your **Hierarchy** view, your UFO parent should look something like this:

30_UFOParent


|   |                                                                                                                                                                                                                                 |   |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
|   | When making these empty game objects , Unity may set the Z-Position to a random number. Remember making new empty game objects, you typically want to go through and manually check their position, or set it to 0,0,0. |   |
|   |                                                                                                                                                                                                                                 |   |

For now, we'll keep things simple and set the position of the parent and the patrol points to `0,0,0`.

In your UFO's state graph, rename `Dormant` to instead say `Patrol` - this way we won't have to change the transitions. Like before, consider what we're trying to implement:

```text
We need a way to remember if we are going to our first point or not. Either way, we need to select either patrol point A or B, and move towards them. Ideally, we should also be moving at our regular move speed
```

There are a few ways you could implement this logic, but we recommend the following:

* Use the Select Node to toggle between point A and B
* Have a Transform variable for both Patrol point A and B
* Create a boolean variable to help track which point you are moving to
* You will need a way to change the value of this boolean
  * You could do this using a trigger on the patrol point (using OnTriggerEnter)
    * You may need a new layer for this, if you use a new layer make sure to check the **Layer Collision Matrix**
    * Not recommended, but it's possible to do this without making a new layer, ask your TA about `Collider - Layer Overrides`
  * You could also do this by checking the distance from the player to the patrol point

Move your Patrol points to random positions in your game view (in a line from one another is fine), and test to see how it works. It is recommended to increase your UFO's `moveSpeed`(the fact you can change this so easily shows how important making variables are).

To check your solution, here is an implementation using the trigger method mentioned above (I made sure to tick collisions between UFO and the PatrolPoint layer in the **Layer Collision Matrix**). Just note that the other method, using distance, would simply involve checking if the distance between the UFO and a PatrolPoint is within a small range (like 0.2).

<img src="PracResources\images\31_PatrolSolution.png">


<img src="PracResources\images\32_PatrolPointSolution.png">

From here, there are several ways you could expand on your State machine. As we mentioned before, these are another tool in the toolbox, but can be quite powerful and helpful when making complex systems, without requiring a lot of "spaghetti" code.

We'd recommend at attempting at least one challenge for the week. Specifically, we'd like you to try adding the **Scanning** state, as it involves adding in a basic timer, an important concept in games.

## Recommended Challenge: Recreate the Dormant state, as "Scanning"

We might want to recreate the dormant state, but instead lets make it a bit more interesting and have it be the "Scanning" state.

Regarding transition logic, we want to transition from this state from only the patrol state, but from this state we want to enter both the Patrol and the Chase state.

Here are some notes:

* You will need a new variable, called scanningTimer (float)
* You will need to subtract time from this (using DeltaTime)
* Once/if this timer reaches zero, we want to transition to the patrol state
* We need to be able to transition to Chase at any time, based on the default logic (if the player is within the detection radius)

## Moderate Challenge: From Chase, return to nearest Patrol Point

Simply put, after returning from the `Chase` state to the Patrol State, compare the distance of `PatrolPointA` and `PatrolPointB`, and move to the one with the smallest distance.

Some notes:

* You can do this by simply changing the value of the boolean based on the distance (using an If node)
* This logic could probably be done using the `OnEnter` event node that already exists in your Patrol state.

## Intermediate Challenge: Change MoveTowards to "Lerp"

Instead of using the `MoveTowards` node, look into using the `Lerp` node. To make this a bit more interesting, this must be implemented using the following rule:

* Make the UFO speed up or slow down dynamically, based on how close it is to the player, using `Lerp`.
  * The UFO should speed up the further the player gets

## Intermediate Challenge: Make a third/fourth/fifth patrol point

Restructure your Patrol state to be able to handle multiple patrol points - to successfully implement this, we do not want you to use a boolean to check which point to go to next.

Some notes:
* You may need to consider using an index value - google this term if you're not sure what it is.
* While a List would be a sensibly way to do this, using a list is out of scope and not something we want you to do

## Hard Challenge: Rotate the UFO towards the player while moving to a target

Currently our UFO never rotates, and simply travels straight towards the player. While in theory it sounds like it would be trivial to have the UFO rotate, and also move towards the player, with our current logic, the heading that the UFO would need to travel would be different (think about how you would move towards something that is behind you, as opposed to turning around and walking forward -- the operation is different)

Have a look at your `Chase` state in your state graph, and try and implement both movement and rotation into On Update:

* You will likely need to use InverseTransformDirection to obtain a heading/direction
* You will probably want to use our IsOnLeft node to figure out which way to rotate the UFO

You may also want to implement this functionality into your Patrol points.

## Hard Challenge: Making your own States

Surprise us, make something unique and novel. Ideally we want something not listed here, but here are some ideas to help get the ball rolling:

* Make the UFO shoot out asteroids
* Make a UFO spawner instead of a UFO
* After completing a patrol between two points, randomise the patrol point locations

## Completed Tasks

To verify that you've completed all of the content in this prac, check you have done the following:

* A UFO with different states and transitions between them
* The UFO is able to move between patrol points
* The UFO can chase the player if it gets too close
* One of the challenges listed above

### Show your demonstrator

To show your understanding of this weeks content to your prac demonstrator, you may wish to show:

* Gameplay featuring the Player and the UFO
* Which challenge you picked and why
* Your State Graph
