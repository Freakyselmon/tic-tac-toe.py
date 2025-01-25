
Tic-Tac-Toe Game 🎮❌⭕
Welcome to the classic Tic-Tac-Toe game, built using Python and Tkinter! This graphical version of the beloved game offers an engaging and user-friendly experience.

Features ✨
Intuitive GUI: Smooth gameplay on a clean 3x3 grid interface.
Two-Player Mode: Player 1 uses 'X' and Player 2 uses 'O'.
Dynamic Scoring: Keeps track of wins, losses, and ties across multiple rounds.
Game Modes:
Reset Game: Clears the board while retaining scores.
New Game: Resets both the board and scores.
Winning Logic: Automatically detects wins, losses, or ties and displays the result with custom messages.
Responsive Status Updates: Informs players whose turn it is with visually distinct colors.
Gameplay 🎲
Players take turns clicking on the grid to place their mark (X or O).
The first player to get 3 marks in a row, column, or diagonal wins.
If all spaces are filled without a winner, the game declares a tie.
Technology Used 💻
Python: Core logic and game functionality.
Tkinter: Graphical user interface for a seamless experience.
NumPy: Efficient handling of the game board and logic calculations.
How to Play:
Run the program.
Click on the grid to start playing.
Use the "Reset Game" or "New Game" buttons as needed.
Ready to relive the joy of Tic-Tac-Toe? Challenge a friend and see who dominates the board! 🚀

from tkinter import *
import numpy as np

# Constants
BOARD_SIZE = 600
SYMBOL_SIZE = 80
SYMBOL_THICKNESS = 20
SYMBOL_X_COLOR = '#EE4035'
SYMBOL_O_COLOR = '#0492CF'
GREEN_COLOR = '#7BC043'


class TicTacToe:
    def __init__(self):
        self.window = Tk()
        self.window.title('Tic-Tac-Toe')
        self.canvas = Canvas(self.window, width=BOARD_SIZE, height=BOARD_SIZE)
        self.canvas.pack()

        # Initialize game variables
        self.player_X_turns = True
        self.board_status = np.zeros((3, 3))  # 0: empty, -1: X, 1: O
        self.gameover = False
        self.X_wins = False
        self.O_wins = False
        self.tie = False

        self.X_score = 0
        self.O_score = 0
        self.tie_score = 0

        # Add a status label
        self.status_label = Label(self.window, text="Player 1 (X)'s Turn", font="cmr 20 bold", fg=GREEN_COLOR)
        self.status_label.pack()

        # Add buttons
        self.reset_button = Button(self.window, text="Reset Game", font="cmr 15 bold", command=self.reset_game)
        self.reset_button.pack(side=LEFT, padx=20, pady=10)

        self.new_game_button = Button(self.window, text="New Game", font="cmr 15 bold", command=self.new_game)
        self.new_game_button.pack(side=RIGHT, padx=20, pady=10)

        self.initialize_board()

        # Bind mouse click
        self.canvas.bind('<Button-1>', self.click)

    def mainloop(self):
        self.window.mainloop()

    def initialize_board(self):
        self.canvas.delete("all")
        # Draw grid lines
        for i in range(1, 3):
            self.canvas.create_line(i * BOARD_SIZE / 3, 0, i * BOARD_SIZE / 3, BOARD_SIZE, width=2)
            self.canvas.create_line(0, i * BOARD_SIZE / 3, BOARD_SIZE, i * BOARD_SIZE / 3, width=2)

    def reset_game(self):
        """Resets the game board and keeps the scores."""
        self.initialize_board()
        self.player_X_turns = True
        self.board_status = np.zeros((3, 3))
        self.gameover = False
        self.X_wins = False
        self.O_wins = False
        self.tie = False
        self.status_label.config(text="Player 1 (X)'s Turn", fg=GREEN_COLOR)

    def new_game(self):
        """Starts a new game and resets the scores."""
        self.reset_game()
        self.X_score = 0
        self.O_score = 0
        self.tie_score = 0

    def draw_O(self, logical_position):
        grid_position = self.convert_logical_to_grid_position(logical_position)
        self.canvas.create_oval(
            grid_position[0] - SYMBOL_SIZE, grid_position[1] - SYMBOL_SIZE,
            grid_position[0] + SYMBOL_SIZE, grid_position[1] + SYMBOL_SIZE,
            width=SYMBOL_THICKNESS, outline=SYMBOL_O_COLOR
        )

    def draw_X(self, logical_position):
        grid_position = self.convert_logical_to_grid_position(logical_position)
        self.canvas.create_line(
            grid_position[0] - SYMBOL_SIZE, grid_position[1] - SYMBOL_SIZE,
            grid_position[0] + SYMBOL_SIZE, grid_position[1] + SYMBOL_SIZE,
            width=SYMBOL_THICKNESS, fill=SYMBOL_X_COLOR
        )
        self.canvas.create_line(
            grid_position[0] - SYMBOL_SIZE, grid_position[1] + SYMBOL_SIZE,
            grid_position[0] + SYMBOL_SIZE, grid_position[1] - SYMBOL_SIZE,
            width=SYMBOL_THICKNESS, fill=SYMBOL_X_COLOR
        )

    def display_gameover(self):
        if self.X_wins:
            self.X_score += 1
            text = 'Winner: Player 1 (X)'
            color = SYMBOL_X_COLOR
        elif self.O_wins:
            self.O_score += 1
            text = 'Winner: Player 2 (O)'
            color = SYMBOL_O_COLOR
        else:
            self.tie_score += 1
            text = 'It\'s a tie'
            color = 'gray'

        self.canvas.delete("all")
        self.canvas.create_text(BOARD_SIZE / 2, BOARD_SIZE / 3, font="cmr 60 bold", fill=color, text=text)

        score_text = f'Scores \nPlayer 1 (X): {self.X_score} \nPlayer 2 (O): {self.O_score} \nTie: {self.tie_score}'
        self.canvas.create_text(BOARD_SIZE / 2, 3 * BOARD_SIZE / 4, font="cmr 30 bold", fill=GREEN_COLOR,
                                text=score_text)

        self.gameover = True

    def convert_logical_to_grid_position(self, logical_position):
        """Convert logical position (row, col) to grid position (x, y)."""
        row, col = logical_position
        x = (col * (BOARD_SIZE / 3)) + (BOARD_SIZE / 6)
        y = (row * (BOARD_SIZE / 3)) + (BOARD_SIZE / 6)
        return x, y

    def convert_grid_to_logical_position(self, grid_position):
        logical_position = (np.array(grid_position) // (BOARD_SIZE / 3)).astype(int)
        return logical_position[1], logical_position[0]  # Flip for [row, col]

    def is_grid_occupied(self, logical_position):
        return self.board_status[logical_position[0]][logical_position[1]] != 0

    def is_winner(self, player):
        player_value = -1 if player == 'X' else 1

        # Check rows and columns
        for i in range(3):
            if np.all(self.board_status[i, :] == player_value) or np.all(self.board_status[:, i] == player_value):
                return True

        # Check diagonals
        if np.all(np.diag(self.board_status) == player_value) or np.all(np.diag(np.fliplr(self.board_status)) == player_value):
            return True

        return False

    def is_tie(self):
        return np.all(self.board_status != 0)

    def click(self, event):
        if self.gameover:
            return

        grid_position = [event.x, event.y]
        logical_position = self.convert_grid_to_logical_position(grid_position)

        if not self.is_grid_occupied(logical_position):
            if self.player_X_turns:
                self.draw_X(logical_position)
                self.board_status[logical_position[0]][logical_position[1]] = -1
                self.status_label.config(text="Player 2 (O)'s Turn", fg=SYMBOL_O_COLOR)
            else:
                self.draw_O(logical_position)
                self.board_status[logical_position[0]][logical_position[1]] = 1
                self.status_label.config(text="Player 1 (X)'s Turn", fg=SYMBOL_X_COLOR)

            self.player_X_turns = not self.player_X_turns

            # Check for winner or tie
            if self.is_winner('X'):
                self.X_wins = True
                self.status_label.config(text="Player 1 (X) Wins!", fg=SYMBOL_X_COLOR)
                self.display_gameover()
            elif self.is_winner('O'):
                self.O_wins = True
                self.status_label.config(text="Player 2 (O) Wins!", fg=SYMBOL_O_COLOR)
                self.display_gameover()
            elif self.is_tie():
                self.tie = True
                self.status_label.config(text="It's a Tie!", fg='gray')
                self.display_gameover()


# Run the game
if __name__ == "__main__":
    game = TicTacToe()
    game.mainloop()
