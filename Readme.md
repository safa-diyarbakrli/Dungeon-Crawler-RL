Dungeon Crawler RL

    This project is a custom Reinforcement Learning environment of a dungeon crawler game. The agent learns to navigate a dungeon while avoiding lava pits, collect loot, defeat bosses, and save the princess. Using Gymnasium and Pygame, the environment is built to simulate the game dynamics. Unlike standard grid worlds which typically involve simple navigation (Point A to Point B), this environment introduces state dependencies and combat logic. 

Environment Design

- The Map

````
```
MAP = [
    "+-------------+",
    "|M: : : : :B|F|",
    "| : : : : : : |",
    "|M: |-:-: : |B|",
    "|R:B| : : :-| |",
    "|-:-| : :M: :B|",
    "| : : :-:-: :-|",
    "|P: : : :R:M:L|",
    "+-------------+",
]
```
````
    The environment is built on a 7x7 grid containing:
    •	Walls (|, -): Impassable barriers defining the ground layout.
    •	Restricted Areas (R): "Lava" tiles.
    •	Entities:
        o	Player (P): The learning agent.
        o	Loot (L): A weapon required to damage the main boss.
        o	Mini-Boss (M): An enemy that grants a damage power-up.
        o	Main Boss (B): The primary antagonist blocking the victory condition.
        o	Finish Line (F): The exit point where the princess is waiting to get rescued.
State Space 

    The game has 25,088 possible states .The logic behind it:
    •	Player Position: 49 possible positions (7x7 grid).
    •	Loot Position: 4 possible positions .
    •	Mini-Boss Position: 4 possible positions .
    •	Main Boss Position: 4 possible positions .
    •   Loot Status: 2 possible states (collected or not).
    •   Mini-Boss Status: 2 possible states (alive or dead).
    •   Main Boss Status: 2 possible states (alive or dead).
so the total number of states is 49 * 4 * 4 * 4 * 2 * 2 * 2 = 25,088


  ![res1](dungeon_animation.gif)

- Reward system:
    The reward system is as follows:
    - -1 for each time step taken
    - +10 for collecting the loot
    - -10 for fighting the mini-boss without the loot
    - +20 for defeating the mini-boss
    - -10 for fighting the main boss without the mini-boss' power-up
    - +20 for defeating the main boss
    - +30 for saving the princess
