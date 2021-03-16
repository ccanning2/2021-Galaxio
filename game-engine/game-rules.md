# The Game

The Game for Entelect Challenge 2021 is **Galaxio**.

In a match your **1** ship will compete against a **variable** amount of other ships in an ever decreasing circular map. The goal is to be the last ship alive.


## Contents
- [The Game](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#the-game)
    - [The Map](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#the-map)
        - [Structure](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-structure)
        - [Visibility](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-visibility)
        - [Boundary](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-boundary)
    - [Objects](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#objects)
        - [Food](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-food)
        - [Wormholes](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-wormholes)
        - [Gas Clouds](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-gas-clouds)
        - [Asteroid Clouds](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-asteroid-clouds)
    - [The Ship](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#the-ship)
        - [Speed](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-speed)
        - [Afterburner](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-afterburner)
    - [Collisions](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#collisions)
        - [Ship to ship collisions](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-ship-to-ship-collisions)
        - [Food collisions](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-food-collisions)
    - [Game Tick Payload](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#game-tick-payload)
        - [GameObjects](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#-gameobjects)
    - [The Commands](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#the-commands)
        - [All Commands](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#all-commands)
        - [Command Structure](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#command-structure)
        - [Command: FORWARD](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#command:-forward)
        - [Command: STOP](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#command:-stop)
        - [Command: START_AFTERBURNER](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#command:-start_afterburner)
        - [Command: STOP_AFTERBURNER](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#command:-stop_afterburner)
    - [Endgame](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#endgame)
    - [Scoring](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#scoring)
    - [Math](https://github.com/EntelectChallenge/2021-Galaxio/blob/master/game-engine/game-rules.md#math)

## The Map

The Map is a cartesian plane stretching out in both positive and negative directions. The map will only deal with whole numbers. Your ship can only be in an integer x,y position on the map. The map center will be 0,0 and the edge will be provided as a radius.

The map contains the following objects:

* Players - yourself and the other opponents.
* Food - small objects that you need to collect to grow in size.
* Wormholes - two points on the map that are connected in both directions.
* Gas clouds - harmful zones that will hurt you while traversing through.
* Asteroid fields - hazardous zones that will slow you down you while traversing through.

### Visibility

The entire map will be visible at all times and will shrink as the boundary decreases.

### Boundary

The size of the map will shrink each game tick. Objects that fall outside of the map at the end of a game tick will be removed from the map. This excludes players, being outside of the map will reduce the players size by 1 each game tick.

* The map size reduction is the final action of the game tick.

## Objects

All objects are represented by a circular shape and has a centre point with X and Y coordinates and a radius that defines it size and shape.
### Food

The map will be scattered with food objects of size 3 which can be consumed by players.

* Food will not move.
* Food will be removed if it falls outside of the map.
* If a player collides with food it will consume the food in it's entirety and the player will increase by the same size as the food.

### Wormholes

Wormholes exist in pairs, allowing a player's ship to enter one side and exit the other. Wormholes will grow each game tick to a set maximum size, when traversed, the wormhole will shrink by half the size of the ship traversing through it.

* Traversal will only be possible if the wormhole is larger than the ship.
* Traversing the wormhole is instantaneous, no penalties are applied to the player for using a wormhole.
* Momentum and direction is maintained through the wormhole.
* If one end of the wormhole pair falls outside of the map both will be destroyed.
* Wormhole pairings are not given but rather will need to be mapped by players through trial and error.

### Gas Clouds

Gas clouds will be scattered around the map in groupings of smaller clouds. Ships can traverse through gas clouds, however once a ship is colliding with a asteroid cloud it will be reduced in size by 1 each game tick. Once a ship is no longer colliding with the gas cloud the effect will be removed.

### Asteroid Fields

Asteroid fields will be scattered around the map in groupings of smaller clouds. Ships can traverse through asteroid fields, however once a ship is colliding with a gas cloud its speed will be reduced by a factor of 2. Once a ship is no longer colliding with the asteroid field the effect will be removed.

## The Ship

Your bot is playing as a circular spaceship, that feeds on planetary objects and other ships to grow in size. Your ship begins with the following values:

* **Speed** - your ship will move x positions forward each game tick, where x is your speed. Your speed will start at 20 and decrease as the ship grows.
* **Size** - your ship will start with a radius of 10.
* **Heading** - your ship will move in this direction, between 0 and 359 degrees.
* **State** - your ship will be able to toggle it's afterburner on or off for as long as they are willing to keep it on.

Your ship will not begin moving until you have given it a command to do so. Once given the first forward command it will continue moving at the ship's current speed or heading until the stop command is given.

### Speed

Speed determines how far forward your ship will move each game tick. Speed is inversely linked to size, a larger ship will move slower. Speed is determined by the following formula:
```
200/bot.size = speed
```
With the result being rounded to ceiling and with a minimum of 1.

**Note** the value 200 comes from the Speeds.Ration in the appsettings.json and may change during balancing.


### Afterburner

The afterburner will increase your ship's speed by a factor of 2. However this will also start reducing the size of your ship by 1 per tick.

* An active afterburner can cause self destruction if the ship's size becomes less than 5.

## Collisions

A collision will occur when two objects overlap by at least one unit of world space. This means that objects can touch each other, but the moment they overlap by a single world space unit they will be considered colliding and collision mechanics will apply.

### Ship to ship collisions

When player ships collide, the ship with the larger size will consume the smaller ship at the rate of 50% of the consumer's size to a maximum of the consumee's size.

### Food collisions

When a ship collides with a food particle, the ship will consume the food in its entirety and will increase in size by the size of the food it just collided with.

## Game Tick Payload

All players will receive the state of the map and all objects at the start of each tick. The payload of each game tick will contain the following information:

```
{
  "World": {
      "CenterPoint": {
        "X": 0,
        "Y": 0
      },
      "Radius": 1000,
      "CurrentTick": 0
    },
    "GameObjects": {
      "8b77d46b-2844-48c4-a3f3-179de15776a3": [ 3, 0, 0, 2, 42, 225 ],
      "2b75d46b-2866-48f1-a4g5-179de15779o0": [ 3, 0, 0, 2, 234, -900 ],
      "9b34d46b-4844-48g2-a3b2-179de15771m8": [ 3, 0, 0, 2, -100, 189 ],
      ...
    }
}
```

### GameObjects

The GameObjects list contains all objects on the map. The list contains of a guid for each object and the objects data.

The order of the data will not change, and is as follows:
* Size - The radius of the object
* Speed - The speed it is able to move at
* Heading - The direction it is looking towards
* GameObjectType - The type of object
    * 1: Player
    * 2: Food
    * 3: Wormhole
    * 4: Gas Cloud
    * 5: Asteroid Field
* X Position - The x position on the cartesian plane
* Y Position - The y position on the cartesian plane


## The Commands

In each game tick each player can submit one command for their ship.

* All player commands are validated before executing any commands. Invalid commands (eg. Invalid syntax) result in the command being ignored and your ships maintaining it's current state.
* All player's commands are executed at the same time (in a single game tick), and not sequentially.

### All Commands

* FORWARD
* STOP
* START_AFTERBURNER
* STOP_AFTERBURNER

### Command Structure

Commands can be sent to the engine as often as you like, but the engine does not wait for commands from the bots and processes the game state at a set rate.

The Runner will only allow a maximum of one command per tick.

Your commands will be lodged against your bot for processing during the next tick. These actions are processed under FIFO (First in, First out), meaning your earliest sent action is processed first. However, this list of actions should only ever be one command long.

This means that your bot does not need to send a command each tick and can take as long as it wants to send each command. Feel free to run all the clever artificial intelligence you like! Just note that other bots might still be sending commands as there is no wait time.
### Command: FORWARD

```
FORWARD 238
```

This command will start moving your ship in the direction provided in degrees.

### Command: STOP

```
STOP
```

This command will stop moving your ship this game tick and until another movement command is issued.

### Command: START_AFTERBURNER

```
START_AFTERBURNER
```

This command activates your ship's afterburner.

### Command: STOP_AFTERBURNER

```
STOP_AFTERBURNER
```

This command deactivates your ship's afterburner.

## Endgame

The last ship alive is the winning ship and thus the winning bot.

* Should the final two ships both die at the same time, the ship with the highest score will be the winner.

## Scoring

A score will be kept for each player which will be visible once the match is over. This will only be used for tie breaking.

* Consuming another player's ship = 10 points
* Consuming food = 1 point
* Traversing a wormhole = 1 point

## The Math
This section is to explain the general math used for the movement and placement calculations by the engine, as well as basic functions to calculate this. These formulas have been included as functions in each starter bot.

The world uses the standard mathematical cartesian plane with the 0 degree at the rightmost axis and it positively increments counter clockwise as can be seen below:
![cartesian plane](https://github.com/Jana-Wessels/images/blob/master/cartesian_plane.png?raw=true)

### Distance Calculation
Distance is calculated using the standard pythagoras theorem, a diagram showing the values can be seen below:

![cartesian plane](https://github.com/Jana-Wessels/images/blob/master/distance_calculation.png?raw=true)

Direction does not matter in the distance calculation as we are only concerned with the distance between two objects regardless of where they are.
Where delta y and delta x:
```
ΔX = X1 - X2
ΔY = Y1 - Y2
```
It does not matter if the delta's are negative as that will be cancelled out in the formula through squaring them. The formula for the distance calculation is as follows:

```
distance = Math.Sqrt((deltaX) + (deltaY))
```
This formula can be used to calculate the distance between objects in the world. Note this calculation gives the distance between the centrepoints of the objects so their radius is not taken into account with this calculation.

### Direction Calculation
Direction is calculated using the arctan2 formula to get the direction between two objects. Note this formula returns a value between -180 and 180, and so an adjustment formula is explained further below.

The formula also returns the direction in radians see below formula to convert radians to degrees, note the engine only accepts degrees:
```
private int toDegrees(radians)
{
    return Math.Round((radians * (180 / Math.PI)), 0);
}
```

The arctan2 calculation to find the direction from object 1 to object 2 is as follows, note that the order of the values is important otherwise you will get the direction from object 2 to object 1 which will be inverted by 180 degrees.
```
direction_inRadians = Math.Atan2(Y2 - Y1, X2 - X1)

direction_inDegrees = toDegrees(direction_inRadians)
```

This now need to be corrected for the possible negative values, this can be done by adding 360° to the calculated degrees and modding it by 360. This will return a value between 0° and 360° which equates to the same cardinality as the calculated value.

```
corrected_direction_inDegrees = (cartesianDegrees + 360) % 360;
 
```

*Example:
-150° will be corrected to 210° which when referring to the image of the cartesian plane above is in the same position, note the placement of a negative heading would be clockwise from 0°.


**Note:**
The values provided within this readme are subject to change during balance phases.
Entelect has a best effort approach to maintaining this readme file. However the most accurate values can be found in appsettings.json within the Game Engine repository.

