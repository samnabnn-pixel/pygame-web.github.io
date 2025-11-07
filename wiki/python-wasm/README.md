import pygame
import math
import random

# --- Initialize PyGame ---
pygame.init()
WIDTH, HEIGHT = 800, 500
win = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Stickman Friend Alive!")
FPS = 30
clock = pygame.time.Clock()

WHITE = (255, 255, 255)
BLACK = (0, 0, 0)

# --- Stickman properties ---
stickman = {
    "x": 400,
    "y": 250,
    "size": 20,
    "dx": 2,          # normal speed
    "arm_angle": 0,
    "arm_dir": 1,
    "leg_angle": 0,
    "leg_dir": 1,
    "target": (random.randint(50, 750), random.randint(50, 450)),
    "state": "calm",  # calm, funny, energetic
    "state_timer": 0
}

# --- Draw stickman ---
def draw_stickman(s):
    x, y, size = s["x"], s["y"], s["size"]

    # Head
    pygame.draw.circle(win, BLACK, (int(x), int(y)), size, 2)

    # Body
    pygame.draw.line(win, BLACK, (x, y+size), (x, y+size*3), 2)

    # Arms
    ra_x = x + size*1.5 * math.cos(math.radians(s["arm_angle"]))
    ra_y = y + size*1.5 * math.sin(math.radians(s["arm_angle"]))
    la_x = x - size*1.5 * math.cos(math.radians(s["arm_angle"]))
    la_y = y + size*3 - size*1.5 * math.sin(math.radians(s["arm_angle"]))
    pygame.draw.line(win, BLACK, (x, y+size*1.5), (ra_x, ra_y), 2)
    pygame.draw.line(win, BLACK, (x, y+size*1.5), (la_x, la_y), 2)

    # Legs
    rl_x = x + size * math.cos(math.radians(s["leg_angle"]))
    rl_y = y + size*3 + size * math.sin(math.radians(s["leg_angle"]))
    ll_x = x - size * math.cos(math.radians(s["leg_angle"]))
    ll_y = y + size*3 - size * math.sin(math.radians(s["leg_angle"]))
    pygame.draw.line(win, BLACK, (x, y+size*3), (rl_x, rl_y), 2)
    pygame.draw.line(win, BLACK, (x, y+size*3), (ll_x, ll_y), 2)

# --- Update stickman ---
def update_stickman(s):
    # Swing arms
    s["arm_angle"] += s["arm_dir"] * 3
    if s["arm_angle"] > 45 or s["arm_angle"] < -45:
        s["arm_dir"] *= -1

    # Swing legs
    s["leg_angle"] += s["leg_dir"] * 5
    if s["leg_angle"] > 30 or s["leg_angle"] < -30:
        s["leg_dir"] *= -1

    # Update state every 2 seconds
    if s["state_timer"] <= 0:
        s["state"] = random.choices(
            ["calm", "funny", "energetic"],
            weights=[3, 2, 1]
        )[0]
        s["state_timer"] = FPS * 2  # hold state 2 seconds
    else:
        s["state_timer"] -= 1

    # Adjust speed based on state
    if s["state"] == "calm":
        speed = 1
    elif s["state"] == "funny":
        speed = 2
    else:  # energetic
        speed = 4

    # Occasionally jump if energetic
    if s["state"] == "energetic" and random.randint(1, 100) == 1:
        s["y"] -= 30  # jump up
    else:
        # Move toward target smoothly
        tx, ty = s["target"]
        dx = tx - s["x"]
        dy = ty - s["y"]
        distance = math.hypot(dx, dy)
        if distance > 1:
            s["x"] += dx / distance * speed
            s["y"] += dy / distance * speed
        else:
            s["target"] = (random.randint(50, 750), random.randint(50, 450))

# --- Main loop ---
running = True
while running:
    clock.tick(FPS)
    win.fill(WHITE)
    
    update_stickman(stickman)
    draw_stickman(stickman)
    
    pygame.display.update()
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

pygame.quit()



[Contribute to this page](https://github.com/pygame-web/pygame-web.github.io/edit/main/wiki/python-wasm/README.md)
