```
import random
import os
import time
import sys
import select
import tty
import termios

class Tetris:
    def __init__(self):
        self.width = 10
        self.height = 20
        self.board = [['.' for _ in range(self.width)] for _ in range(self.height)]
        self.current_piece = None
        self.current_x = 0
        self.current_y = 0
        self.game_over = False
        self.score = 0
        self.pieces = [
            [[0, 1, 1, 0], [0, 1, 1, 0], [0, 0, 0, 0], [0, 0, 0, 0]],  # Square
            [[0, 0, 0, 0], [1, 1, 1, 1], [0, 0, 0, 0], [0, 0, 0, 0]],  # I
            [[0, 0, 0, 0], [0, 1, 1, 1], [0, 1, 0, 0], [0, 0, 0, 0]],  # T
            [[0, 0, 0, 0], [0, 1, 1, 0], [0, 0, 1, 1], [0, 0, 0, 0]],  # S
            [[0, 0, 0, 0], [0, 1, 1, 0], [1, 1, 0, 0], [0, 0, 0, 0]],  # Z
            [[0, 0, 0, 0], [1, 1, 1, 0], [0, 0, 1, 0], [0, 0, 0, 0]],  # L
            [[0, 0, 0, 0], [0, 1, 1, 1], [0, 1, 0, 0], [0, 0, 0, 0]]   # J
        ]

    def draw_board(self):
        os.system('cls' if os.name == 'nt' else 'clear')
        print(f"Score: {self.score}")
        display = [row[:] for row in self.board]
        for y in range(4):
            for x in range(4):
                if self.current_piece[y][x] == 1:
                    board_y = self.current_y + y
                    board_x = self.current_x + x
                    if 0 <= board_y < self.height and 0 <= board_x < self.width:
                        display[board_y][board_x] = '#'
        for row in display:
            print(' '.join(row))
        print()

    def can_move(self, new_x, new_y, piece=None):
        if piece is None:
            piece = self.current_piece
        for y in range(4):
            for x in range(4):
                if piece[y][x] == 1:
                    board_x = new_x + x
                    board_y = new_y + y
                    if board_y >= self.height or board_x < 0 or board_x >= self.width:
                        return False
                    if board_y >= 0 and self.board[board_y][board_x] != '.':
                        return False
        return True

    def move_down(self):
        if self.can_move(self.current_x, self.current_y + 1):
            self.current_y += 1
            return True
        return False

    def place_piece(self):
        for y in range(4):
            for x in range(4):
                if self.current_piece[y][x] == 1:
                    board_y = self.current_y + y
                    board_x = self.current_x + x
                    if 0 <= board_y < self.height and 0 <= board_x < self.width:
                        self.board[board_y][board_x] = '#'

    def can_place_piece(self):
        return self.can_move(self.current_x, self.current_y)

    def rotate_piece(self):
        rotated = [[0 for _ in range(4)] for _ in range(4)]
        for y in range(4):
            for x in range(4):
                rotated[x][3 - y] = self.current_piece[y][x]
        if self.can_move(self.current_x, self.current_y, rotated):
            self.current_piece = rotated

    def clear_lines(self):
        lines_cleared = 0
        y = self.height - 1
        while y >= 0:
            if all(cell == '#' for cell in self.board[y]):
                lines_cleared += 1
                for yy in range(y, 0, -1):
                    self.board[yy] = self.board[yy - 1][:]
                self.board[0] = ['.' for _ in range(self.width)]
            else:
                y -= 1
        self.score += lines_cleared * 100

    def is_data_ready(self):
        return select.select([sys.stdin], [], [], 0) == ([sys.stdin], [], [])

    def run(self):
        old_settings = termios.tcgetattr(sys.stdin)
        try:
            tty.setcbreak(sys.stdin.fileno())
            self.current_piece = random.choice(self.pieces)
            self.current_x = self.width // 2 - 2
            self.current_y = 0

            while not self.game_over:
                self.draw_board()
                start_time = time.time()
                while time.time() - start_time < 0.5:
                    if self.is_data_ready():
                        key = sys.stdin.read(1)
                        if key == '\x1b':  # Escape sequence for arrow keys
                            next_char = sys.stdin.read(2)
                            if next_char == '[D' and self.can_move(self.current_x - 1, self.current_y):  # Left
                                self.current_x -= 1
                            elif next_char == '[C' and self.can_move(self.current_x + 1, self.current_y):  # Right
                                self.current_x += 1
                            elif next_char == '[B':  # Down
                                if not self.move_down():
                                    self.place_piece()
                                    self.clear_lines()
                                    self.current_piece = random.choice(self.pieces)
                                    self.current_x = self.width // 2 - 2
                                    self.current_y = 0
                                    if not self.can_place_piece():
                                        self.game_over = True
                            elif next_char == '[A':  # Up (rotate)
                                self.rotate_piece()
                    time.sleep(0.01)
                if not self.move_down():
                    self.place_piece()
                    self.clear_lines()
                    self.current_piece = random.choice(self.pieces)
                    self.current_x = self.width // 2 - 2
                    self.current_y = 0
                    if not self.can_place_piece():
                        self.game_over = True
        finally:
            termios.tcsetattr(sys.stdin, termios.TCSADRAIN, old_settings)
            os.system('cls' if os.name == 'nt' else 'clear')
            print(f"Game Over! Score: {self.score}")

if __name__ == "__main__":
    game = Tetris()
    game.run()

```