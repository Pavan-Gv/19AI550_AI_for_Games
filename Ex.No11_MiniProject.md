# Ex.No: 11  Mini Project 
### DATE:                                                                            
### REGISTER NUMBER : 212221240024
### AIM: 
Develop an AI for Connect 4 that plays optimally by using the Minimax algorithm with alpha-beta pruning, aiming to make the best possible moves to either win or prevent the opponent from winning.


### Algorithm:
	step 1.	Define Board and Helper Functions: Set up the board and functions to check valid moves, drop pieces, and check for a win.
	step 2.	Create Evaluation Function: Score the board based on potential winning alignments.
	step 3.	Implement Minimax with Alpha-Beta Pruning: Recursively simulate moves, maximizing for the AI and minimizing for the opponent, with pruning.
	step 4.	Determine Best Move: Use Minimax to evaluate each column and choose the highest-scoring move.
	step 5.	Integrate into Game Loop: Alternate turns, check for win/draw conditions, and stop if the game ends.
### Program:
~~~
# NumPy is a library for numerical operations in Python.
# It provides support for large, multi-dimensional arrays and matrices, along with mathematical functions.
import numpy as np

# Math is a built-in Python library that provides mathematical functions and operations.
# In this case, it is imported for general mathematical operations.
import math

# Random is a built-in Python library for generating pseudo-random numbers.
# It provides functions for random number generation, shuffling sequences, and making random choices.
import random
# Defining our constants

# Constants representing the dimensions of the game board
ROW_COUNT = 6
COLUMN_COUNT = 7

# Constants representing player and AI indices
PLAYER = 0
AI = 1

# Constants representing player and AI game pieces on the board
PLAYER_PIECE = 1
AI_PIECE = 2
# Function to create an empty game board.
def create_board():
    board = np.zeros((ROW_COUNT, COLUMN_COUNT))
    return board

# Function to drop a game piece into a specified column.
def drop_piece(board, row, col, piece):
    board[row][col] = piece

# Function to check if a column is a valid location for placing a piece.
def is_valid_location(board, col):
    return board[ROW_COUNT-1][col] == 0

# Function to find the next open row in a given column.
def get_next_open_row(board, col):
    for r in range(ROW_COUNT):
        if board[r][col] == 0:
            return r

# Function to print the game board with flipped orientation.
def print_board(board):
    flipped_board = np.flip(board, 0)
    rows, cols = flipped_board.shape

    flipped_board = flipped_board.astype(int)

    for i in range(rows):
        print("|", end="")
        for j in range(cols):
            print(f"{flipped_board[i, j]:2}", end=" |")
        print()

    print("-" * (cols * 4))

    print("|", end=" ")
    for j in range(cols):
        print(f"{j}", end=" | ")

# Function to check if a player has a winning move on the board.
def winning_move(board, piece):
    # Check horizontal locations
    for c in range(COLUMN_COUNT-3):
        for r in range(ROW_COUNT):
            if board[r][c] == piece and board[r][c+1] == piece and board[r][c+2] == piece and board[r][c+3] == piece:
                return True
    # Check vertical locations for win
    for c in range(COLUMN_COUNT):
        for r in range(ROW_COUNT-3):
            if board[r][c] == piece and board[r+1][c] == piece and board[r+2][c] == piece and board[r+3][c] == piece:
                return True

    # Check positively sloped diagonals
    for c in range(COLUMN_COUNT-3):
        for r in range(ROW_COUNT-3):
            if board[r][c] == piece and board[r+1][c+1] == piece and board[r+2][c+2] == piece and board[r+3][c+3] == piece:
                return True
    # Check negatively sloped diagonals
    for c in range(COLUMN_COUNT-3):
        for r in range(3 ,ROW_COUNT):
            if board[r][c] == piece and board[r-1][c+1] == piece and board[r-2][c+2] == piece and board[r-3][c+3] == piece:
                return True

# Function to get a list of valid locations for placing a piece.
def get_valid_locations(board):
    valid_locations = []
    for col in range(COLUMN_COUNT):
        if is_valid_location(board, col):
            valid_locations.append(col)
    return valid_locations
def score_position(board, piece):
    score = 0
    opponent_piece = PLAYER_PIECE

    # Evaluation board for scoring positions on the actual game board.

    # TODO: Fill in values for this board. Give higher values to the positions that you think
    # lead to more wins, and lower values to the positions you think can result in less winning combinations.
    evaluation_board = np.array([[0, 0, 0, 0, 0, 0, 0],
                                 [0, 0, 0, 0, 0, 0, 0],
                                 [0, 0, 0, 0, 0, 0, 0],
                                 [0, 0, 0, 0, 0, 0, 0],
                                 [0, 0, 0, 0, 0, 0, 0],
                                 [0, 0, 0, 0, 0, 0, 0]])

    # Calculate scores for the given player's and opponent's pieces on the board.
    piece_score = np.sum(evaluation_board[board == piece])
    opponent_score = np.sum(evaluation_board[board == opponent_piece])

    # Calculate the final score by subtracting the opponent's score from the player's score.
    score = piece_score - opponent_score
    return score

# Function to check if a game board is a terminal node (end of the game).
def is_terminal_node(board):
    return winning_move(board, PLAYER_PIECE) or winning_move(board, AI_PIECE) or (len(get_valid_locations(board)) == 0)

# Minimax algorithm with Alpha-Beta Pruning for finding the best move on the game board.
def minimax(board, depth, alpha, beta, maximizingPlayer):
    valid_locations = get_valid_locations(board)
    is_terminal = is_terminal_node(board)

    # Base case: If the depth is zero or the game is over, return the current board's score.
    if depth == 0 or is_terminal:
        if is_terminal:
            if winning_move(board, AI_PIECE):
                return (None, math.inf)
            elif winning_move(board, PLAYER_PIECE):
                return (None, -math.inf)
            else: # Game is over, no more valid moves
                return (None, 0)
        else: # Depth is zero
            return (None, score_position(board, AI_PIECE))

    # Maximize the score if it's the maximizing player's turn
    if maximizingPlayer:
        value = -math.inf
        column = random.choice(valid_locations)
        for col in valid_locations:
            row = get_next_open_row(board, col)
            b_copy = board.copy()
            drop_piece(b_copy, row, col, AI_PIECE)
            new_score = minimax(b_copy, depth-1, alpha, beta, False)[1]

            # Update the best move and alpha value.
            if new_score > value:
                value = new_score
                column = col
            alpha = max(alpha, value)

            # Prune the search if the alpha value is greater than or equal to beta.
            if alpha >= beta:
                break
        return column, value

    else: # Minimize the score if it's the minimizing player's turn.
        value = math.inf
        column = random.choice(valid_locations)
        for col in valid_locations:
            row = get_next_open_row(board, col)
            b_copy = board.copy()
            drop_piece(b_copy, row, col, PLAYER_PIECE)
            new_score = minimax(b_copy, depth-1, alpha, beta, True)[1]

            # Update the best move and beta value.
            if new_score < value:
                value = new_score
                column = col
            beta = min(beta, value)

            # Prune the search if the alpha value is greater than or equal to beta
            if alpha >= beta:
                break
        return column, value
# Create an empty game board.
board = create_board()

# Initialize game state variables.
game_over = False
turn = random.randint(PLAYER, AI)

# Print the initial game board.
print_board(board)
print("\n")

# Main game loop
while not game_over:
    # Player 1's turn
    if turn == PLAYER:
        # Get player input for column selection.
        col = int(input("Player 1 Make your selection (0-6):"))
        print("\n")
        # Check if the selected column is a valid location.
        if is_valid_location(board, col):
            # Find the next open row in the selected column and drop the player's piece.
            row = get_next_open_row(board, col)
            drop_piece(board, row, col, PLAYER_PIECE)

            # Check if Player 1 has a winning move.
            if winning_move(board, PLAYER_PIECE):
                print("You win, congratulations!")
                game_over = True
                print_board(board)
                break

        # Print the current game board and switch to the next turn
        print_board(board)
        print("\n")
        turn += 1
        turn= turn % 2

    # Player 2's turn (AI)
    if turn == AI and not game_over:
        input("Press Enter for Player 2 to go: ")
        print("\n")
        # Use the minimax algorithm to find the best move for the AI.
        col, minimax_score = minimax(board, 3, -math.inf, math.inf, True)

        # Check if the selected column is a valid location.
        if is_valid_location(board, col):
            # Find the next open row in the selected column and drop the AI's piece.
            row = get_next_open_row(board, col)
            drop_piece(board, row, col, AI_PIECE)

            # Check if Player 2 (AI) has a winning move.
            if winning_move(board, AI_PIECE):
                game_over = True
                print_board(board)
                print("\n")
                print("The AI wins, better luck next time!")
                break

        # Print the current game board and switch to the next turn.
        print_board(board)
        print("\n")
        turn += 1
        turn= turn % 2
~~~












### Output:

<img width="609" alt="Screenshot 2024-11-14 at 9 23 41 PM" src="https://github.com/user-attachments/assets/c04be3b3-c8db-4c83-9049-2f943fde5902">


<img width="299" alt="Screenshot 2024-11-14 at 9 27 59 PM" src="https://github.com/user-attachments/assets/2610c455-acfc-47c5-a8cb-bdb24f1f76ae">

### Result:
Therefore Connect 4 game  developed with the Minimax algorithm and alpha-beta pruning, effectively makes optimal moves to maximize its winning chances, block opponent wins, and play strategically, resulting in competitive, efficient gameplay.
