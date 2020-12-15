# Programming Styles -- WiSe 20-21
---------------

# Minesweeper

All the assignments of the Programming Style WiSe 20-21 consist of re-implementing the same program over and over, using a different programming style every time. We follow the same approach proposed by Cristina Lopes in her book *"Exercises in Programming Style."*

The program we will implement is a single-player, interactive puzzle-game called "Minesweeper," which is modeled after the classic video-game available on old PCs. The first iteration of Minesweeper, as we know it, was built by Robert Donner, who was hired by Microsoft in 1989. The game is now one of the most iconic computer games next to Pong, Tetris, and PacMan, to name a few.

According to [Wikipedia](https://en.wikipedia.org/wiki/Minesweeper_(video_game)), there are many variants of this game. Below I summarize the rules of the variant you will implement for the assignments. In particular, **we will not make use of the mouse to interact with the game**. Instead, we will play by reading the inputs from the console.

In this document, I will describe the specifications of the Minesweeper program from [this](http://www.freeminesweeper.org/help/minehelpinstructions.html) and [this other](https://www.wikihow.com/Play-Minesweeper) links. You can find more information about grading [here](GRADING.md).

> **NOTE**: these specifications might be incomplete or unclear. For example, the handling of unexpected inputs or corner cases might not be fully specified. If you see anything that we must fix, instead of complaining about it, pro-actively correct the problem. Open issues, propose updates, refinements, and write more public test cases (perfect for corner cases!!).

In this initial assignment, you implement a basic version of the game. However, in the later assignments, you will be asked to implement add-os and "enhancements" to it. 

>**NOTE**: All the changes to the specifications will be also notified on StudIP.

## Goal and Rules of the Game

The game's objective is to clear **a rectangular board** containing hidden "mines" or bombs without detonating any of them. The players can make use of clues about the number of neighboring mines in each field. So a number on a square indicates how many mines are around that square. For example, if two squares are touching each other and one of the squares has "1" on it, you know that the square next to it has a mine beneath it.

If the player selects a square containing a bomb, the game is over, and the player has lost it. Otherwise, if the player manages to select all the squares (without clicking on any bombs), the game is won.

In some versions of Minesweeper, the first move is "protected," that is, the boards are set up by never placing a mine on the first square revealed. For the assignment, we do not implement such a rule. It does not matter whether the player detonates a mine after the first move or the subsequent moves. 

### Game Setup
Each game starts with  **a rectangular board** of **unmarked squares**. Some squares contain mines others do not. The board's size and the number and placement of the mines are specified in a text file passed as an input parameter.
The program takes **ONLY** this file as an input parameter. If more than one input parameter is provided, only the first one
is considered, the remaining ones are ignored.

> Note: different programming languages handle command-line arguments differently. You have to deal with this in your programs.

The file must follow this format:
1. It must contain only text. The number of lines identifies the board's height, while the length of the lines identifies its width.
2. Each line follows this format: a `.` indicates an empty square, a `*` identifies a mine. No other characters (except newline [\n]) shall be present in this file.
3. There are no empty lines (except for the last line). 
4. All the lines must have the same length and must be terminated by [\n] (**INCLUDING THE LAST ONE!**).

A valid file defining a 3x3 board containing 2 mines might look like this:

```
..*[\n]
...[\n]
..*[\n]
```

A similar but non-valid file instead is:

```
..*[\n]
...[\n]
..*
```

This file is non-valid because it violates the constraint #4 (all lines must be terminated by '\n').

Valid boards must have more than one square and do not contain only mines.
However, a board **without mines** is valid, provided the other constraints on symbols and size hold. To clarify, a valid board can have zero or more mines, but it cannot have all the squares filled with mines.

The board configuration files must have a `name` and the `.cfg` extension (see more details in the "Invalid Filenames" section).


#### Maximum Board Size

In theory, the Minesweeper should work with any (valid) board sizes, but for practical reasons (visualization, testing), we set the following limit for the board: **20 x 20** (see below)

So valid boards cannot have columns or rows greater than "20" squares.

```
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │   │[\n]
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘[\n]
```

> **Note**: boards of size '1 x 20' and '20 x 1' are valid, despite they might not be that interesting to play.

### Playing the game

The game is usually played using the mouse. The left mouse button is used to uncover (or reveal) **covered** squares, while the right mouse button is used to flag **covered** squares that potentially contain mines. 

For the assignment, **we do not rely on the mouse to interact with the game. Instead, we input the square's coordinates that we intend to click and a character that identifies the action to perform on those squares using the command line. We use `R` to reveal squares and `F` to flag them**. Clicking or flagging squares that have been already **uncovered** does not cause any change. Nevertheless, the GUI must be refreshed as a consequence to process the input.

In the following, "(left-)click" means *input the coordinate of the square with action `R`*, while "flag" or "(right)-click" mean *input the coordinate of the square with action `F`*.

If a player uncovers a mine, the mine detonates, and the game is lost. Otherwise, the uncovered squares display either a number, indicating the number of mines adjacent to it, or a "shaded" block if there are no mines in the neighboring squares. After left-clicking a square with zero adjacent mines, all its "neighbors" **covered** squares are automatically uncovered if they do not contain mines.

Players' job is to use the numbers in the **uncovered** squares to figure out which of the remaining **covered** squares contain mines and which are safe to click.

> **NOTE**: A squares' "neighbors" are the squares adjacent above, below, left, right, and all four diagonals. Squares on the sides of the board or in a corner have *fewer* neighbors. The board does not wrap around the edges.

> **NOTE**:  If the player uncovers a square with no neighboring bombs, minesweeper automatically reveals all the non-mined adjacent squares. It does so **transitively**, so a large area of the board might open, and in some cases, the play can win the game after a single move.

Flagging a **covered** square will cause a flag symbol (`¶`) to appear on it. 

> **NOTE**: right-clicking a **covered** square toggles the flag symbol on it. So, if the square is not flagged, right-clicking it will make the flag symbol appear. Otherwise, if the square is already flagged, right-clicking it will make the flag symbol disappear.

Flagged squares are still **covered**. Hence, players can left-click on them to uncover them. There is no need to "unflag" squares before uncovering them. Additionally, there is no predefined limit on the number of flags that can be used. So, a player can flag all the **covered** squares if (s)he wishes.

So squares can be **covered** or **uncovered**. A covered square can be blank or flagged. An uncovered square can be empty, can contain a number, or can contain a mine.

### End of the Game

A game ends when the player uncovers all non-mine squares (win) or a mine is detonated (lost). Flagging all the mined squares is not required to finish the game; however, all the mines must be clearly shown on the board when the game is lost.

## The Text-based Graphical User Interface (GUI)
The GUI of the game is made entirely by characters, like in very old games. The reason for doing so is that it is easier to test whether your implementation is correct or not automatically. There are no colors, but you need to generate the right shapes (see below).

Minesweeper text-based GUI is composed of three parts: 
the board, 
the message banner, 
and the console input. 

### The board 
The board is a "box/table" aligned to the left (so no space on the left), made of cells. Each cell corresponds to a square. Squares are indexed starting from **(1,1)** on the top left corner to *(N,M)* on the bottom right corner. The first coordinate identifies the row, the second the column. The central character defines the content of the cell.
For **covered** squares, the `¶` symbol indicates that they are flagged, while 
`:nothing:` indicates that they are not flagged.
For **uncovered** squares,  the `*` identifies a mine, `:digit:` is the number of mines in neighboring cells, while the `▓` symbol identified a square with no adjacent mines.

An example of a 3x3 board with those symbols is shown below:

```
┌───┬───┬───┐[\n]
│ * │ 1 │   │[\n]
├───┼───┼───┤[\n]
│   │   │ 2 │[\n]
├───┼───┼───┤[\n]
│   │ ▓ │ ¶ │[\n]
└───┴───┴───┘[\n]
```

In the above board, there is an **uncovered** square that contains a mine in position `(1,1)`, an  **uncovered** square that contains `1` in position `(1,2)` and another  **uncovered** square that contains `2` in position `(2,3)`. The square in position `(3,2)` is **uncovered** and empty (`▓`), while the square in position `(3,3)` is **covered** and flagged. All the remaining squares are **covered** and can be left- or right-clicked.

> **NOTE**: The board above serves only as an illustration; such a configuration would not be possible in practice because a mine detonated.

Here are some specifications of the boards:

* The height of each square is 3 line
* The width of each square is 3 chars
* Nearby squares share borders
* A covered square is "empty"
* An flanges square contains a flag `¶`
* Uncovered squares contain a shaded cell `▓` unless it contains a mine (`*`) or a digit
* Each line is terminated by `\n`, and there are no spaces before or after it

### The message banner
During the game, Minesweeper might output messages to the player. For example, if the provided input is invalid, or the game is over. Messages are displayed inside the message banner, which is positioned below the board.

> **NOTE**: There are no empty lines between the board and the message banner.

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

Both the message banner and the messages are left-justified (no spaces on the left). The message banner must stretch to fit all the messages on one line and cannot be shorter than the board itself.

An example of a short message when the player wins the game is:

```
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║You Won!   ║[\n]
╚═══════════╝[\n]
```

An example of a long message when the player input an invalid input is:

```
└───┴───┴───┘[\n]
╔════════════════════════════════╗[\n]
║The provided input is not valid!║[\n]
╚════════════════════════════════╝[\n]
```

As you might have noted, there is no space on the right side of the message when the message is longer than the banner.

### Console Input

The console input starts with the char `>` (yes, exactly this character!), and a newline does not terminate it.

```
>
```

### All together
Putting the elements of the GUI all together results and assuming a 3x3 initial board results in:

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

### Extended ASCII

Note that you do not need to use the extended ASCII characters to generate the GUI if you can use other encodings.

Please read this [StackOverflow question](https://stackoverflow.com/questions/22273046/how-to-print-the-extended-ascii-code-in-java-from-integer-value). 

## Processing User Inputs

The player inputs three values at a time, and the program reacts to them, refreshing the GUI and potentially showing a message in the message banner.

Inputs must follow the specified format to be considered valid. Invalid inputs are rejected by the system (i.e., they do not cause a change in the state of the board), but Minesweeper notifies the user about them.

### Input format
Valid inputs consist of three values separated by one or more spaces.

The first two values identify a square in the board (target for the action), while the last input identifies the action to perform (i.e., reveal or flag).
Spaces in front and back of the inputs do not matter.
Any input with a different format is considered invalid and reported with the message: `The provided input is not valid!`

Examples of valid inputs are:

`1`` ``1`` ``F`

`1`` ``2`` `` ``F`

`1`` ``1`` `` ``F`` `` `

` `` `` ``1`` ``1`` `` ``F`` `` `

Examples of **invalid** inputs are:

`1`` ``1`` ``1`` `` ``F`` `` ` (more than three inputs)

`111`` `` ``F`` `` ` (only two inputs)

` ``111F`` ` (only one input)

In addition to the format of the inputs,  also the values provided by the player matter.

Valid inputs must have a positive integer less or equal to the number of rows as the first value, a positive integer less or equal to the number of columns as the second value, and either the letter `R` or `F` (capitalized) as the last input. Inputs that do not meet those constraints are invalid and must be reported with the message: `The provided input is not valid!`

Assuming the board is 3x3, the following inputs are invalid:

 `1`` ``1`` ``f` (non capitalized F)
 
 `1`` ``5`` ``R` (5 is invalid as the board contains only 3 columns)

 `-1`` ``1`` ``F` (-1 is not positive)
 
  `1.0`` ``1`` ``F` (1.0 is not integer)
 
### Additional inputs are ignored

Since the program accepts inputs from the standard input, one can try to feed the game with more inputs than necessary. If this happens, no matter the game's final state (WON/LOST), all the additional inputs must be silently ignored.

### No more inputs/no enough inputs

If the user does not provide any more inputs, but the standard input is still open, the game does nothing. However, if the standard input is closed (for example, because we are testing the program); then, the program should end showing the following message:

`Not enough inputs!`

> **Note**: the behavior, in this case, is the same as the win or lost game, except for the message displayed in the banner.

### Do not clear the screen while refreshing the GUI

When the GUI is plotted *Minesweeper* must not clear the screen, but simply output the new UI right after the previous one. The reasons are:

1. Clearing the screen is more complex to implement than simply outputing the UI.
2. Clearing the screen is harder to test, because it depends on many factors (including size of the screen, size of the board, etc.)

## Examples of games
This section exemplifies how the game develops.

### Simple game (win case)

We start the game by pointing it to the board configuration. Assume the following configuration for the board stored in a file called `simple.cfg`. Note that there is only one mine.

```
..*[\n]
...[\n]
...[\n]
```

For the java implementation, invoking the program looks like this:

```
java Minesweeper simple.cfg
```

For the javascript implementation, invoking the program looks like this:

```
node minesweeper.js simple.cfg
```

After starting the program, we should see the following output on the console:

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

We input ` ``1`` ``1`` R` to reveal the top left corner, and cause the GUI to refresh to obtain:

```
┌───┬───┬───┐[\n]
│ ▓ │ 1 │   │[\n]
├───┼───┼───┤[\n]
│ ▓ │ 1 │ 1 │[\n]
├───┼───┼───┤[\n]
│ ▓ │ ▓ │ ▓ │[\n]
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║You Won!   ║[\n]
╚═══════════╝[\n]
```

The input caused the squares adjacent to (1,1) that do not contain a mine to be automatically revealed. Because there are no more squares left to uncover except the one that contains the mine, the game automatically ends. As the message says, the player won the game.

> **NOTE**: Because the game ended, no additional inputs must be provided; hence the input console is NOT shown.


### Simple game (lost case)

We start the game using the same `simple.cfg` used before, but this time we provide `1`` ``3`` R` as the first input to reveal the top right corner.
Since the square contains the mine, we lost the game, and the program shows the following output.

```
┌───┬───┬───┐[\n]
│   │   │ * │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
├───┼───┼───┤[\n]
│   │   │   │[\n]
└───┴───┴───┘[\n]
╔═══════════╗[\n]
║You Lost!  ║[\n]
╚═══════════╝[\n]
```

Once again, since the game is over, the input console does not show anymore. 
Since the game is lost, all and only the mines are shown on the board. The remaining squares are left untouched.

To clarify this point, imagine that the following board configuration is given as input:

```
..*.....
...*....
........
```

After playing as first move `1 1 R`, the game computes the following board (note that the console symbol `>` is *not* reported in the following figure for simplicity):

```
┌───┬───┬───┬───┬───┬───┬───┬───┐[\n]
│ ▓ │ 1 │   │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│ ▓ │ 1 │ 2 │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│ ▓ │ ▓ │ 1 │   │   │   │   │   │[\n]
└───┴───┴───┴───┴───┴───┴───┴───┘[\n]
╔═══════════════════════════════╗[\n]
║                               ║[\n]
╚═══════════════════════════════╝[\n]
```

Next, as after the second move `1 3 R`, the game ends, both the mines are shown, and the appropriate message appears in the banner.

```
┌───┬───┬───┬───┬───┬───┬───┬───┐[\n]
│ ▓ │ 1 │ * │   │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│ ▓ │ 1 │ 2 │ * │   │   │   │   │[\n]
├───┼───┼───┼───┼───┼───┼───┼───┤[\n]
│ ▓ │ ▓ │ 1 │   │   │   │   │   │[\n]
└───┴───┴───┴───┴───┴───┴───┴───┘[\n]
╔═══════════════════════════════╗[\n]
║You Lost!                      ║[\n]
╚═══════════════════════════════╝[\n]
```

> **NOTE**: Only the squares containing mines must be revealed. All the other squares remain untouched.


## Corner cases, Exceptions, and the like

### Invalid Filenames
Any existing file which does not match the following pattern: `<NAME>.cfg` must be considered invalid; hence, *Misesweeper* must exit with exit code is `2`. `<NAME>` must be a valid filename (so no special characters and such).

**Filename has priority over file exists**. *Minesweeper* should check the validity of the input parameter (as String) even before checking the file's existence and validating its content. So if an invalid filename is provided, no matter whether the file exists on not, the exit code is `2` (invalid file) and not exit code `1` (missing file).

**Folders as inputs**. A folder with a valid name (`<NAME>.cfg`) is considered an invalid file *if it exists*; therefore, *Misesweeper* must exit with exit code `2`. Otherwise, if the folder has a valid name but does not exist, *Misesweeper* must exit with exit code `1`.

**CaSeS dO nOt MaTtEr**. When it comes to the file **extension**, the cases do not matter: a file named `<NAME>.CFG` is as good as a file named `<NAME>.CfG`.

Examples of invalid file names are:

* `.cfg` this file does not have the `<NAME>` part, so it is invalid
* `<NAME>`, `<NAME>.` those files do not have a valid extension
* `<NAME>.ccfg`, `<NAME>.blabla`, `<NAME>.foo` are not valid because they have the wrong extension

Example of valid file names are (If you disagree, open an issue on GitHub):

* `<NAME_WITHOUT_SPACES>.cfg` is valid
* `<NAME>..cfg` is valid, because `.` does not invalidate `<NAME>`.
* `<NAME_WITH_AN_ENDING_DOT>.cfg` is just the one above made (hopefully) more clear

> **NOTE**: filenames containing spaces or file paths containing spaces are valid in theory, but since handling them is *tricky*, we assume that this never happens in practice. So both public and private tests always use filenames and file paths **without** spaces.


### Invalid board configuration
If the configuration file is not present, or the file does not contain a valid board definition, the program must end with an error.
If the file is missing, the exit code must be `1`; if the file is not valid, the exit code is `2`.

An empty configuration file is not valid, so the exit code must be `2`. Other invalid files contain characters different from `.` and `*` or do not correspond to rectangular boards. Spaces are NOT allowed in the board configuration files.
Furthermore, an IOException, which is possibly thrown by a file reader, also results in exit code `2`.

No exception messages must be raised ever!

### Additional  cases
In general, additional corner cases might exist. If you find some, please list them here. Ideally, you can define public tests that capture the corner cases, so all the other students can update their code (and you can get bonus points!!)
