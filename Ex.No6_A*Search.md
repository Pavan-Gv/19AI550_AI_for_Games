# Ex.No: 6  Implementation of Zombie survival game using A* search 
### DATE:                           
### Slot: 4H2-1
### Name: G Venkata Pavan Kumar
### REGISTER NUMBER : 212221240013
### AIM: 
To write a python program to simulate the Zomibie Survival game using A* Search 
### Algorithm:
1. Start the program
2. Import the necessary modules
3. Initiate the pygame engine and window
4. Collect the Zombie image and resize it within a display window 
5. Create a Euclidean distance heuristic function to find the distance from current location to Target position
6.  Move the Zombie towards the target by A* search 
7.  In main, create the obstacles and move the player by Key movements up, down,left and right 
10.  Update the display every time 
11.  Stop the program
 ### Program:
```python
import pygame
import time
import math
from queue import PriorityQueue

# Initialize Pygame
pygame.init()

# Window settings (increase window size)
WIDTH, HEIGHT = 750, 750  # Increased window size
WIN = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Zombie Survival Game")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 255, 255)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
OBSTACLE_COLOR = (169, 169, 169)

# Grid settings (decrease grid size)
GRID_SIZE = 25  # Smaller grid for more detailed movement
PLAYER_SPEED = 2  # Speed of player movement
ZOMBIE_SPEED = 1 # Slower zombie movement

# Image sizes
PLAYER_IMG_SIZE = 40  # Resize player and zombie images to fit the smaller grid
ALIEN_IMG_SIZE =40

# Load images
player_img = pygame.transform.scale(pygame.image.load("manshooter.png"), (PLAYER_IMG_SIZE, PLAYER_IMG_SIZE))
zombie_img = pygame.transform.scale(pygame.image.load("alienplant.png"), (ALIEN_IMG_SIZE, ALIEN_IMG_SIZE))

# Game settings
ROWS, COLS = HEIGHT // GRID_SIZE, WIDTH // GRID_SIZE  # Adjust rows and columns based on grid size

# Game variables
player_pos = (1, 1)
zombie_pos = (ROWS - 2, COLS - 2)
# Increased obstacles to 18
obstacles = [
    # Quadrant I (Top-right)
    (16, 25), (16, 26), (16, 27), (17, 27), (18, 27),  # L-shape
    (22, 28), (23, 28), (24, 28), (23, 27),            # T-shape
    (25, 22), (25, 23), (25, 24), (24, 23), (26, 23),  # Plus-shape

    # Quadrant II (Top-left)
    (5, 20), (5, 21), (5, 22), (6, 22), (7, 22),       # L-shape
    (10, 25), (11, 25), (12, 25), (11, 24),            # T-shape
    (8, 28), (8, 29), (8, 30), (7, 29), (9, 29),       # Plus-shape

    # Quadrant III (Bottom-left)
    (3, 3), (3, 4), (3, 5), (4, 5), (5, 5),            # L-shape
    (6, 7), (7, 7), (8, 7), (7, 6),                    # T-shape
    (10, 5), (10, 6), (10, 7), (9, 6), (11, 6),        # Plus-shape

    # Quadrant IV (Bottom-right)
    (20, 5), (20, 6), (20, 7), (21, 7), (22, 7),       # L-shape
    (24, 10), (25, 10), (26, 10), (25, 9),             # T-shape
    (27, 5), (27, 6), (27, 7), (26, 6), (28, 6),       # Plus-shape

    # Scattered additional points to fill in gaps in each quadrant
    (12, 18), (13, 18), (14, 18), (15, 18), (14, 17),  # Quadrant II
    (22, 12), (23, 12), (24, 12), (23, 11),            # Quadrant IV
    (2, 10), (3, 10), (4, 10), (3, 9),                 # Quadrant III
    (17, 20), (18, 20), (19, 20), (18, 19)             # Quadrant I
]
start_time = time.time()
game_over = False
score = 0

# Zombie movement delay variable
zombie_move_delay = 1.5  # Delay in frames for zombie movement
zombie_move_counter = 0  # Counter to track the delay

# Functions for A* search
def h(p1, p2):
    x1, y1 = p1
    x2, y2 = p2
    return abs(x1 - x2) + abs(y1 - y2)

def a_star_search(start, end):
    count = 0
    open_set = PriorityQueue()
    open_set.put((0, count, start))
    came_from = {}
    g_score = {node: float("inf") for row in range(ROWS) for node in [(row, col) for col in range(COLS)]}
    g_score[start] = 0
    f_score = {node: float("inf") for row in range(ROWS) for node in [(row, col) for col in range(COLS)]}
    f_score[start] = h(start, end)

    open_set_hash = {start}

    while not open_set.empty():
        current = open_set.get()[2]
        open_set_hash.remove(current)

        if current == end:
            path = []
            while current in came_from:
                path.append(current)
                current = came_from[current]
            return path[::-1]  # Return reversed path

        neighbors = [(current[0] + i, current[1] + j) for i, j in [(-1, 0), (1, 0), (0, -1), (0, 1)]]
        for neighbor in neighbors:
            if 0 <= neighbor[0] < ROWS and 0 <= neighbor[1] < COLS and neighbor not in obstacles:
                if g_score[current] + 1 < g_score[neighbor]:
                    g_score[neighbor] = g_score[current] + 1
                    f_score[neighbor] = g_score[neighbor] + h(neighbor, end)
                    came_from[neighbor] = current
                    if neighbor not in open_set_hash:
                        count += 1
                        open_set.put((f_score[neighbor], count, neighbor))
                        open_set_hash.add(neighbor)

    return []  # No path found

# Game loop
def main():
    global player_pos, zombie_pos, game_over, score, zombie_move_counter
    clock = pygame.time.Clock()

    while not game_over:
        clock.tick(10)  # Control frame rate
        WIN.fill(WHITE)
        
        # Draw boundary box
        pygame.draw.rect(WIN, RED, (0, 0, WIDTH, HEIGHT), 5)

        # Draw obstacles
        for obs in obstacles:
            pygame.draw.rect(WIN, OBSTACLE_COLOR, (obs[1] * GRID_SIZE, obs[0] * GRID_SIZE, GRID_SIZE, GRID_SIZE))

        # Draw player
        WIN.blit(player_img, (player_pos[1] * GRID_SIZE, player_pos[0] * GRID_SIZE))

        # Draw zombie
        WIN.blit(zombie_img, (zombie_pos[1] * GRID_SIZE, zombie_pos[0] * GRID_SIZE))

        # Timer and Score display
        score = int(time.time() - start_time)
        font = pygame.font.SysFont(None, 35)
        score_text = font.render(f'Score: {score}', True, BLUE)
        WIN.blit(score_text, (10, 10))

        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                return

        # Key state checking for continuous movement with boundary check
        keys = pygame.key.get_pressed()
        if keys[pygame.K_UP] and player_pos[0] > 0 and (player_pos[0] - 1, player_pos[1]) not in obstacles:
            player_pos = (player_pos[0] - 1, player_pos[1])
        if keys[pygame.K_DOWN] and player_pos[0] < ROWS - 1 and (player_pos[0] + 1, player_pos[1]) not in obstacles:
            player_pos = (player_pos[0] + 1, player_pos[1])
        if keys[pygame.K_LEFT] and player_pos[1] > 0 and (player_pos[0], player_pos[1] - 1) not in obstacles:
            player_pos = (player_pos[0], player_pos[1] - 1)
        if keys[pygame.K_RIGHT] and player_pos[1] < COLS - 1 and (player_pos[0], player_pos[1] + 1) not in obstacles:
            player_pos = (player_pos[0], player_pos[1] + 1)
        zombie_move_counter += 1
        if zombie_move_counter >= zombie_move_delay:
            path = a_star_search(zombie_pos, player_pos)
            if path:
                zombie_pos = path[0]  # Move zombie along path
            zombie_move_counter = 0  # Reset counter

        # Check for collision
        if zombie_pos == player_pos:
            game_over = True

        pygame.display.update()

    # Game Over screen
    WIN.fill(WHITE)
    game_over_text = font.render("Game Over", True, RED)
    final_score_text = font.render(f'Final Score: {score}', True, BLUE)
    WIN.blit(game_over_text, (WIDTH // 2 - 50, HEIGHT // 2 - 30))
    WIN.blit(final_score_text, (WIDTH // 2 - 60, HEIGHT // 2 + 10))
    pygame.display.update()
    pygame.time.delay(3000)

# Start game
main()
pygame.quit()
```
### Output:

![image](https://github.com/user-attachments/assets/4fccc2ea-d15c-4d5f-b06b-2d80b8fff693)

![image](https://github.com/user-attachments/assets/5640362f-b66c-4460-9a25-f6767a3b2709)

![image](https://github.com/user-attachments/assets/98832adc-5476-4309-83cd-1f919c19e8a1)


### Result:
Thus the simple Zombie survival game was implemented using python.
