-import pygame
import random

# Определяем константы для размера окна и цветов
SCREEN_WIDTH = 640
SCREEN_HEIGHT = 480
WHITE = (255, 255, 255)
BEIGE = (245, 245, 220)
PINK = (255, 192, 203)

# Определяем класс для игрока
class Player(pygame.sprite.Sprite):
    def _init_(self):
        super()._init_()
        self.image = pygame.Surface((50, 50))
        self.image.fill(PINK)
        self.rect = self.image.get_rect()
        self.rect.x = 100
        self.rect.y = SCREEN_HEIGHT - self.rect.height

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_SPACE] and self.rect.bottom == SCREEN_HEIGHT:
            self.velocity = -15
        self.velocity += 1
        self.rect.y += self.velocity
        if self.rect.bottom > SCREEN_HEIGHT:
            self.rect.bottom = SCREEN_HEIGHT

# Определяем класс для кругов
class Circle(pygame.sprite.Sprite):
    def _init_(self):
        super()._init_()
        self.image = pygame.Surface((50, 50))
        self.image.fill(BEIGE)
        pygame.draw.circle(self.image, WHITE, (25, 25), 25)
        self.rect = self.image.get_rect()
        self.rect.x = SCREEN_WIDTH + random.randint(100, 200)
        self.rect.y = random.randint(50, SCREEN_HEIGHT - 100)

    def update(self):
        self.rect.x -= 5
        if self.rect.right < 0:
            self.kill()

# Инициализируем Pygame
pygame.init()
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Jump Game")

# Создаем группы для спрайтов
all_sprites = pygame.sprite.Group()
circles = pygame.sprite.Group()

# Создаем игрока и добавляем его в группу
player = Player()
all_sprites.add(player)

# Задаем переменные для создания новых кругов
circle_timer = 0
circle_frequency = 60

# Запускаем игровой цикл
clock = pygame.time.Clock()
running = True
while running:
    # Обрабатываем события Pygame
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Обновляем спрайты
    all_sprites.update()

    # Создаем новые круги
    circle_timer += 1
    if circle_timer == circle_frequency:
        circle_timer = 0
        new_circle = Circle()
        all_sprites.add(new_circle)
        circles.add(new_circle)

    # Проверяем столкновение игрока с кругами
    hits = pygame.sprite.spritecollide(player, circles, False)
    if hits:
        running = False

    # Рисуем на экране
    screen.fill(BEIGE)
    all_sprites.draw(screen)
    pygame.display.update()

    # Устанавливаем FPS
    clock.tick(60)

# Завершаем Pygame
pygame.quit()
