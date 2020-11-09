# Programming Styles -- WiSe 20-21
---

# Minesweeper

All the assignments of the Programming Style WiSe 20-21 consist in re-implementing the same program over and over, using a different programming style every time. This follows the same approach proposed by Cristina Lopes in her book *"Exercises in Programming Style."*

The program we will implement is a single-player, interactive puzzle-game called "Minesweeper" which is modeled after the classic video-game available on old PCs. The first iterations of Minesweeper as we know it was built by Robert Donner who was hired by Microsoft in 1989. The game is now one of the most iconic computer games next to Pong, Tetris, and PacMan to name a few.

According to [Wikipedia](https://en.wikipedia.org/wiki/Minesweeper_(video_game)), there are many variants of this game. Below I summarize the rules of the variant you will implement for the assignments. In particular, **we will not make use of the mouse to interact with the game**, instead we will play by reading the inputs from the console.

In this document, I will describe the specifications of the Minesweeper program from [this](http://www.freeminesweeper.org/help/minehelpinstructions.html) and [this other](https://www.wikihow.com/Play-Minesweeper) links. You can find more information about grading [here](GRADING.md).

> **NOTE**: these specifications might be incomplete or unclear. For example, handling of expected inputs or corner cases might not be fully specified. If you see anything to be fixed, instead of complaining about it, pro-actively work to fix the problem. Open issues, propose updates, refinements, and write more public test cases (very good for corner cases!!).

In the initial assignments, you'll be asked to implement a basic version of the game. However, later assignments, you will be asked to implement add-os and "enhancements" to it. 

>**NOTE**: All the changes to the specifications will be also notified on StudIP.

## Goal and Rules of the Game

The objective of the game is to clear **a rectangular board** containing hidden "mines" or bombs without detonating any of them. The players can make use of clues about the number of neighboring mines in each field. So a number on a block indicates how many mines are around that block. For example, if there are two squares touching each other and one of the squares has "1" on it, you know that the square next to it has a mine beneath it.

If the player selects a square containing a bomb, the game is over and the player has lost it. Otherwise, if the player manages to select all the squares (without clicking on any bombs), the game is won.

In some versions of Minesweeper, the first move is "protected," that is the boards are set up by never placing a mine on the first square revealed. For the assignment, we do not implement such a rule. The initial board is "empty" and it does not matter whether or not the players detonates a mine after the first move. 


### Game Setup
Each game starts with  **a rectangular board** of **unmarked squares**. Some contains mines others do not. The size of the board and the number and placement of the mines are specified in a text file passed as input parameter.
The program takes **ONLY** this file as input parameter.

The file must follow this format:
1. It must contain only text. The number of lines identifies the height of the board while the length of the lines identify its width.
2. Each line follows this format: a `.` indicates an empty square, a `*` identifies a mine. No other characters (except new line [\n]) shall be present in this file.
2. There are no empty lines. 
3. All the lines must have the same length and must be terminated by [\n].

A valid file defining a 3x3 board containing 2 mines might look like this:

```
..*[\n]
...[\n]
..*[\n]
```

Valid boards must have more than one square, and do not contain only mines.


### Playing the game

The game is usually played using the mouse to click **covered** squares that don't contain mines (left button) and flag squares that potentially contain mines (right button). For the assignment, **we do not rely on mouse, but input the coordinates of the block we intent to click and the action to perform `R` for reveal and `F` for flag**. Clicking squares that have been already uncovered does not cause any change in the game.

In the following, "(left-)click" means *input the coordinate of the square with action `R`*, while "flag" means *input the coordinate of the square with action `F`*.

A player left-clicks a square to **uncover** it. If a player uncovers a mine, the mine detonates, and the game is lost. Otherwise, the uncovered squares displays either a number, indicating the quantity of mines adjacent to it, or a blank tile. All the adjacent **covered** and empty squares are automatically uncovered. Players' job is to use these numbers to figure out which of the **blank squares** have mines and which are safe to click.

> **NOTE**: A squares' "neighbours" are the squares adjacent above, below, left, right, and all 4 diagonals. Squares on the sides of the board or in a corner have *fewer* neighbors. The board does not wrap around the edges.

Right-clicking on a square will **flag** it, causing a flag (`¶`) to appear on it. Flagged squares are still **covered**, and a player can click on them to uncover them. There is not need to "unflag" squares to uncover them.

So squares can be **covered**, **uncovered** and **flagged**.
A covered square is blank and "clickable", while an uncovered square is exposed. Flagged squares are those marked by the player to indicate a potential mine location. Additionally, squares might be empty, contain a number, or a mine.

### End of the Game

A game ends when the player uncover all non-mine squares (win) or a mine is detonated (lost). Flagging all the mined squares is not required.

## The Text-based Graphical User Interface (GUI)
The GUI of the game is made entirely by characters, like in very old games. This is mostly because it is easy to automatically test whether your implementation is correct or not. There are no colors, but you need to generate the right characters (see below).

Minesweeper text-based GUI is composed of three parts: 
the board, 
the message banner, 
and the console input. 

### The board 
The board is a "box/table" aligned to the left (so no space on the left), made of cells. Each cell corresponds to a square. Squares are indexed starting from **(1,1)** on the top left corner to *(N,M)* on the bottom right corner. The first coordinate identifies the row, the second the column. The content of the cell is the central char:
`*` identifies a mine, `1` (or any digit) is the number of mines in neighboring cells, `:nothing:` (is an uncovered square), `▓` is a covered-but-empty square, while `¶` is a flag.

An example of a 3x3 board with those symbols is show below:

```
┌───┬───┬───┐[\n]
│ * │ 1 │   │[\n]
├───┼───┼───┤[\n]
│   │   │ 2 │[\n]
├───┼───┼───┤[\n]
│   │ ▓ │ ¶ │[\n]
└───┴───┴───┘[\n]
```

In the above board, the mine is in position `(1,1)`, the `1` is in position `(1,2)`, the `2` is in position `(2,3)`, the square in position `(3,2)` is empty (`▓`), while the square in position `(3,3)` is flagged. All the remaining squares are **uncovered**.

> **NOTE**: The board above serves only as illustration, such a configuration would be not possible because a mine has been found.

### The message banner
During the game, Minesweeper might output messages to the player. For example, if the provided input is invalid, or the game is over. Messages are displayed inside the message banner, which is positioned below the board. 

```
┌───┬───┬───┐[\n]
│   │   │   │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║           ║[\n]
╚═══════════╝[\n]
```

Both the message banner and the messages are left justified (no spaces on the left). The message banner must stretch to fit all the messages on one line and  cannot be shorter than the board itself.

An example of short message when the player wins the game is:

```
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║You Win !  ║[\n]
╚═══════════╝[\n]
```

An example of a long message when the player input an invalid input is:

```
└───┴───┴───┘[\n]
╔════════════════════════════════╗[\n]
║The provided input is not valid!║[\n]
╚════════════════════════════════╝[\n]
```

As you might have noted, there is not space also on the right side of the message when the message is longer than the banner.

### Console Input

The console input starts with the char `>` (yes exactly this character!) and it is not terminated by a newline.

```
>
```

### All together
Putting the elements of the GUI all together results and assuming an 3x3 initial board results in:

```
┌───┬───┬───┐[\n]
│   │   │   │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║           ║[\n]
╚═══════════╝[\n]
>
```

## Processing User Inputs

The player inputs three values at the time. The first two values identify a square while the last one, which can be either `R` (reveal) or `F` (flag), identifies the action. Values are separated by one or more spaces. 
Spaces in front and in the tail of the inputs are valid.
Inputs in a different format must be considered invalid.

Examples of valid inputs are:

`1`` ``1`` ``F`

`1`` ``2`` `` ``F`

`1`` ``1`` `` ``F`` `` `

` `` `` ``1`` ``1`` `` ``F`` `` `

Examples of invalid inputs are:

`1`` ``1`` ``1`` `` ``F`` `` `

`111`` `` ``F`` `` `

` ``111F`` `

Valid inputs must have a positive integer less or equal the numbers of column as first value, a positive integer less or equal to the number of rows as second value, and either the letter `R` or `F` (capitalized) as the last input. Inputs that specify any other value is invalid.

Assuming a 3x3 board, the following inputs are invalid:

 `1`` ``1`` ``f`
 
 `1`` ``5`` ``R`
 
 `-1`` ``1`` ``F`
 
  `1.0`` ``1`` ``F`
 

## Corner cases, Exceptions, and the like
