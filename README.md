import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 600, 400
FPS = 30
GRAVITY = 1
BIRD_JUMP = -15
PIPE_VELOCITY = -5
PIPE_SPACING = 150
PIPE_WIDTH = 50
BIRD_RADIUS = 20

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# Create the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Flappy Bird")

# Clock to control the frame rate
clock = pygame.time.Clock()

# Fonts
font = pygame.font.SysFont(None, 55)

# Functions
def draw_bird(x, y):
    pygame.draw.circle(screen, WHITE, (x, y), BIRD_RADIUS)

def draw_pipe(pipe):
    pygame.draw.rect(screen, WHITE, pipe)

def display_score(score):
    score_text = font.render(str(score), True, WHITE)
    screen.blit(score_text, (WIDTH // 2 - 20, 20))

def game_over():
    game_over_text = font.render("Game Over", True, WHITE)
    screen.blit(game_over_text, (WIDTH // 2 - 100, HEIGHT // 2 - 50))
    pygame.display.flip()
    pygame.time.delay(2000)
    pygame.quit()
    sys.exit()

# Game variables
bird_y = HEIGHT // 2
bird_velocity = 0

pipes = []

score = 0

# Game loop
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                bird_velocity = BIRD_JUMP

    # Update bird position and velocity
    bird_y += bird_velocity
    bird_velocity += GRAVITY

    # Generate new pipes
    if len(pipes) == 0 or pipes[-1][0] <= WIDTH - WIDTH // 2:
        pipe_height = random.randint(50, HEIGHT - 150)
        top_pipe = pygame.Rect(WIDTH, 0, PIPE_WIDTH, pipe_height)
        bottom_pipe = pygame.Rect(WIDTH, pipe_height + PIPE_SPACING, PIPE_WIDTH, HEIGHT - pipe_height - PIPE_SPACING)
        pipes.append((top_pipe, bottom_pipe))

    # Update pipes
    for i, (top_pipe, bottom_pipe) in enumerate(pipes):
        top_pipe.x += PIPE_VELOCITY
        bottom_pipe.x += PIPE_VELOCITY
        draw_pipe(top_pipe)
        draw_pipe(bottom_pipe)

        # Check for collision with bird
        if top_pipe.colliderect(bird_x, bird_y - BIRD_RADIUS, 2 * BIRD_RADIUS, 2 * BIRD_RADIUS) or \
                bottom_pipe.colliderect(bird_x, bird_y - BIRD_RADIUS, 2 * BIRD_RADIUS, 2 * BIRD_RADIUS) or \
                bird_y - BIRD_RADIUS <= 0 or bird_y + BIRD_RADIUS >= HEIGHT:
            game_over()

        # Check for passing pipes
        if top_pipe.x + PIPE_WIDTH < bird_x - BIRD_RADIUS:
            score += 1
            pipes.pop(i)

    # Clear the screen
    screen.fill(BLACK)

    # Draw bird
    draw_bird(WIDTH // 4, bird_y)

    # Display score
    display_score(score)

    # Update the display
    pygame.display.flip()

    # Cap the frame rate
    clock.tick(FPS)

