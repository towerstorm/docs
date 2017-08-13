
Want to create your own maps / minions / towers / abilities in Tower Storm? You've come to the right place. 

Tower Storm is a community driven game, it's all Open Source (Apache2 Licensed). So you can play with friends, host your own server, even create your own whole new TD game with very little programming knowledge required. 

## Getting Started

If you don't have a copy of Tower Storm running take a look at the [Getting Started instructions in the Tower Storm git repository](https://github.com/towerstorm/game#getting-started).  

All game config code is inside the `/config` folder. You'll want to use the Tower Storm `Sandbox` mode to see your changes. After making changes you only need to refresh your browser to see them in action. 

## Minions

All minion config files are in the `/config/minions` folder. Each minion is its own .js file. I recommend copy + pasting an existing minion to get started quickly. 

### Config Variables

The order of these variables doesn't matter. 

* minionType - String - *required* - This must be the same as the javascript file name. It will probably be removed soon because it's pretty redundant. 
* name - String - *required* - The properly capitalized name of your minion. This is what will appear in the lobby and UI tooltips. 
* description - String - A description for your minion. This will appear in the lobby and in game tooltips.  
* image - String - *required* - The name of the image file to load for this minion. The image should be placed in `/frontend/assets/img/minions/<name>.png`. You also create a minion icon and place that in the minion-icons folder with the same name. The icon is a single frame and will be used for the picture in the lobby and sidebar. 
* width - Integer -  How wide the minion is in-game.
* height - Integer - How tall the minion is in-game. 
* health - Integer - *required* - How much health the minion has. 
* speed - Integer - *required* - The speed of the minion
* value - Integer - How much gold the minion gives when it is killed
* cost - Integer - *required* - How much it costs to create this minion
* souls - Integer - How many souls are required to create this minion  
* income - Integer - How much income this minion gives. 
* moveType - String - can be `ground` or `air`. Air units can only be attacked by towers that can shoot air and vice versa. 
* frames - Object -  An object describing the frames of various animations on the minion
```
{
    up: Array
    down: Array
    left: Array
    right: Array
    special: Array #optional
}
```

## Towers

All tower config files are in the `/config/towers` folder. Each tower is its own .js file. I recommend copy + pasting an existing tower to get started quickly. 

### Config Variables

The order of these variables doesn't matter. 

#### Basic Variables
* id - String - *required* - This must be the same as the javascript file name. It will probably be removed soon because it’s pretty redundant.
* name - String - required - The properly capitalized name of your tower. This is what will appear in the lobby and UI tooltips.
* description - String - A description for your tower. This will appear in the lobby and in game tooltips.
* cost - Integer - *required* - The cost of the tower.
* damageMethod - String - This can be blank or `percent`. If it is left blank the tower deals a normal damage amount. If it is percent then the tower deals a percent of the minions health as damage instead of a flat amount. 
* attackSpeed - Integer - *required* - The number of times this tower attacks per second. 
* image - String - *required* - The name of the image file to load for this tower. The image should be placed in /frontend/assets/img/towers/<name>.png. You also create a tower icon and place that in the tower-icons folder with the same name. The icon is a single frame and will be used for the picture in the lobby and sidebar.
* zIndex - the depth of the image of the tower. Useful for towers on the road that you want to appear beneath minions.  
* bullet - String - *required* - This is the name of the bullet file (located in `/config/bullets`) that is shot by this tower. 
* range - Integer - *required* - The range of the tower in grid squares (64 pixels). A range of 3 = 64 * 3 = 192 pixels. 
* auraRange - Integer - *required* - The range of the aura that this tower gives off. See the `Tower Auras` section below for information on adding an aura to your tower.
* attackMoveTypes - Array - *required* - All the moveType's of minions it can attack. Should be `['ground']`, `['ground', 'air']` or `['air']`. 
* bulletSpawnOffsets - Object - If you want the bullet to not spawn on the center of the tower you can specify it's pixel offset here. This is an object that has an item for every degree that the tower is shooting in and it's relevant offset. E.g.
```
{
    '0': {x: -21, y: -9},
    '10': {x: -22, y: -14},
}
```
This means when the tower is at 0 degrees of rotation the bullet will spawn 21 pixels to the left and 9 up. When the tower is at 10 degrees of rotation it will spawn 22 pixels to the left and 14 up. 
* levels - Array - This is explained in the `Tower Levels Array` section of this document (below).
* doesNotShoot - Boolean - If this is true the tower doesn't shoot as normal (it might have another special ability).
* buildsOnRoads - Boolean - If this true the Tower can only be built on roads, not on the grass. Useful for towers like spikes or a whirlwind that go directly on the minions path. 
* singleBullet - Boolean - This makes it so that the tower constantly spawns one bullet over and over instead of shooting them out at enemies. This is useful for say the flamethrower which just has a flame coming out of it. 
* attackAngles - Array - This makes it so the tower can only shoot on certain angles. So the array [0, 90, 180, 270] will make a tower that can only shoot enemies above, below, to the left or right of it. 
* shootVFX - String - When the tower shoots it will spawn this visual effect over the tower. This must reference a VFX file located in the `/config/vfx.js` files.

#### Tower Levels Array

This is for specifying how strong the tower is and what special abilities it gets as you upgrade it. The first item in the array is level 0 (so at spawn), the next item is for level 1 etc. Each item in the levels array looks something like this:
```
{
    damage: 50,
    cost: 200
}
```
To get to this level the player must pay 200 gold. When the tower gets to this level it will have 50 damage total (NOT 50 additional damage).

You can also add special abilities to towers through the levels array, this is explained below.

#### Tower Special Abilities

To give towers special abilities you must add them to the levels array. This looks like the following:
```
levels: [
  {
    mods: {
      tower: {
        'corpse-explosion': [25, 1.5]
      }
    }
  },
  {
    cost: 80,
    mods: {
      tower: {
        'corpse-explosion': [30, 1.6]
      }
    }
  }
]
```
With this tower when it is spawned it has the special ability `corpse-explosion` with the values of 25 and 1.5. You can see this ability in `/game/lib/game/modifiers/tower`. The value of 25 is the damagePercent which is how much of the minions life is dealt to nearby mnions when the corpse explodes. The second parameter of 1.5 is the `explosionRadius` which is the radius of minions that get damaged when the corpse explodes. 

When the tower is level 2 it has a corpseExplosion ability but this time it deals 30% of the minions life as damage, and has a radius of 1.6.

#### Tower Auras

Tower Auras are similar to special abilities (above) but they power up nearby towers and give them special abilities instead. Here's an example of Auras in the level's array:
```
 levels: [
  {
    auras: {
      gold: [0.5]
    }
  },
  {
    cost: 80,
    auras: {
      gold: [1]
    }
  }
]
```
This gives the tower the "gold" aura. This aura can be found in `/game/lib/game/auras/tower/`. This aura makes it so all nearby towers get a bonus 0.5 gold per kill at level 1 and a bonus 1 gold per kill at level 2. 

 
#### Variables for Animation 
* idleFrames - Integer - This is how many frames will be looped through when the tower is idle. For example you might have a tower that just always rotates on the spot even when it's not shooting.  
* totalRotationFrames - Integer - This is how many different directions the tower shoots in, how many different directional frames it has. 
* totalFramesPerAttack - Integer - This is how many frames each attack animation takes up, not including the idle animation. 
* attackFrames - Array - This is an array of the order of attack animations.  

For example: Take a look at the Cannon Tower. It has 36 frames of animation. As you can see in the image it actually has 108 frames. This is because each direction has 3 frames - 1 for idle, 2 for attack. So it has a `totalRotationFrames` value of 36. a `totalFramesPerAttack` value of 3 and an `attackFrames` array of [2, 1, 0] because when it's attacking it plays the 2nd frame of the rotational group, then the 1st frame, then the 0th. 

## Glossary

* Income - Every x seconds you gain gold equal to your income level. Generally sending minions increases your income. This encourages you to attack the enemy even with weaker units that you know are going to die and creates early, middle, late game. 
* Souls - Souls are a resource in Tower Storm. They are like supply in other RTS games. You get souls at the start of the game and they replenish over time. You can also collect the souls of dead minions to replenish them faste.r 
