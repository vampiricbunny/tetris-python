# tetris-python
simple proof of concept for python tetris to show I can read write and use python.



Explanation of the Tetris Implementation


# Game size:
Game Board: The game uses a 20x10 grid (board) to represent the play area. Each cell is either empty (.) or occupied by a tetromino (#).
Tetrominoes: Seven standard tetromino shapes (Square, I, T, S, Z, L, J) are defined as 4x4 arrays in the pieces list. A random piece is selected when a new piece is needed.
Game Loop: The run method manages the game loop, which:

Draws the current state of the board.
Handles user input for moving or rotating the piece.
Moves the piece down every 0.5 seconds.
Places the piece when it cannot move further, clears completed lines, and spawns a new piece.


Input Handling: The game uses select and tty for non-blocking input detection in a Unix-like environment (Linux/Mac). For Windows, it may require adjustments (e.g., using msvcrt). The controls are:

# Controls: 
Left Arrow: Move piece left.
Right Arrow: Move piece right.
Down Arrow: Move piece down faster.
Up Arrow: Rotate piece clockwise.

# Collision Detection:
Collision Detection: The can_move and can_place_piece methods check if a piece can move to a new position or be placed without overlapping existing blocks or exceeding board boundaries.
Rotation: The rotate_piece method rotates the current piece 90 degrees clockwise by transforming its 4x4 grid, applying the rotation only if valid.
Line Clearing: The clear_lines method checks for completed rows, removes them, shifts the board down, and increments the score by 100 points per line.
Game Over: The game ends if a new piece cannot be placed at the starting position.


# How to Play (and rules)
How to Play

Run the Program: Save the code as tetris.py and run it with Python 3 (e.g., python tetris.py) in a terminal on a Unix-like system (Linux/Mac). For Windows, you may need to modify the input handling (e.g., using msvcrt).
Controls:

Left Arrow: Move piece left.
Right Arrow: Move piece right.
Down Arrow: Move piece down faster.
Up Arrow: Rotate piece clockwise.


Objective: Stack tetrominoes to form complete horizontal lines, which are cleared to increase your score. The game ends when a new piece cannot be placed.


## Notes:: 
Notes

This is a console-based style implementation for simplicity. 
For a graphical version, consider using a library like Pygame.
The game speed is controlled by a 0.5-second interval for automatic piece descent. 

Adjust the time.sleep value in the game loop to change the speed.
The input handling is optimized for Unix-like systems. For Windows compatibility, you may need to replace the select/tty logic with msvcrt or run in a compatible environment like WSL.
The score increments by 100 per cleared line. You can enhance this by adding bonuses for multiple lines cleared simultaneously.


On the last note remember this is just a proof of concept.