import pygame
import sys

# Inicialização
pygame.init()

# Configurações da janela
WIDTH, HEIGHT = 800, 600
WINDOW = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Construção Livre Minimalista")

# Configurações do grid
TILE_SIZE = 40
GRID_WIDTH, GRID_HEIGHT = 100, 100  # tamanho do mundo em blocos
camera_x, camera_y = 0, 0

# Cores dos blocos
BLOCK_TYPES = {
    0: None,  # vazio
    1: (100, 200, 100),  # grama
    2: (150, 150, 150),  # pedra
    3: (160, 100, 50),   # terra
    4: (100, 150, 255)   # água
}
current_block = 1

# Mundo vazio
world = [[0 for _ in range(GRID_WIDTH)] for _ in range(GRID_HEIGHT)]

# Loop principal
clock = pygame.time.Clock()
running = True

while running:
    clock.tick(60)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        # Seleção de blocos
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_1:
                current_block = 1
            elif event.key == pygame.K_2:
                current_block = 2
            elif event.key == pygame.K_3:
                current_block = 3
            elif event.key == pygame.K_4:
                current_block = 4

        # Colocar / remover blocos
        if pygame.mouse.get_pressed()[0]:  # botão esquerdo
            mx, my = pygame.mouse.get_pos()
            grid_x = (mx + camera_x) // TILE_SIZE
            grid_y = (my + camera_y) // TILE_SIZE
            if 0 <= grid_x < GRID_WIDTH and 0 <= grid_y < GRID_HEIGHT:
                world[grid_y][grid_x] = current_block

        if pygame.mouse.get_pressed()[2]:  # botão direito
            mx, my = pygame.mouse.get_pos()
            grid_x = (mx + camera_x) // TILE_SIZE
            grid_y = (my + camera_y) // TILE_SIZE
            if 0 <= grid_x < GRID_WIDTH and 0 <= grid_y < GRID_HEIGHT:
                world[grid_y][grid_x] = 0

    # Movimento da câmera
    keys = pygame.key.get_pressed()
    speed = 10
    if keys[pygame.K_a]:
        camera_x -= speed
    if keys[pygame.K_d]:
        camera_x += speed
    if keys[pygame.K_w]:
        camera_y -= speed
    if keys[pygame.K_s]:
        camera_y += speed

    # Desenhar o mundo
    WINDOW.fill((230, 230, 230))
    start_x = camera_x // TILE_SIZE
    start_y = camera_y // TILE_SIZE
    end_x = start_x + WIDTH // TILE_SIZE + 2
    end_y = start_y + HEIGHT // TILE_SIZE + 2

    for y in range(start_y, end_y):
        for x in range(start_x, end_x):
            if 0 <= x < GRID_WIDTH and 0 <= y < GRID_HEIGHT:
                block = world[y][x]
                if block != 0:
                    rect = pygame.Rect(x * TILE_SIZE - camera_x,
                                       y * TILE_SIZE - camera_y,
                                       TILE_SIZE, TILE_SIZE)
                    pygame.draw.rect(WINDOW, BLOCK_TYPES[block], rect)
                    pygame.draw.rect(WINDOW, (0, 0, 0), rect, 1)

    # Mostrar tipo de bloco atual
    font = pygame.font.SysFont(None, 30)
    text = font.render(f"Bloco atual: {current_block}", True, (0, 0, 0))
    WINDOW.blit(text, (10, 10))

    pygame.display.flip()

pygame.quit()
sys.exit()

