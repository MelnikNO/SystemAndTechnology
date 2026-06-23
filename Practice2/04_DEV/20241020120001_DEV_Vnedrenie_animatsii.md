# DEV - Внедрение анимации

**Теги:** #DEV #mechanics #pygame #animation

**Цель:** Реализация анимаций для персонажей и объектов окружения, чтобы сделать геймплей более отзывчивым и визуально привлекательным.

**Основные анимации для главного героя:**
1.  **Idle (Покой):** Небольшое покачивание персонажа, когда игрок не двигается.
2.  **Walk (Ходьба):** Цикл перебора ног (8 кадров).
3.  **Jump (Прыжок):** Специфическая поза в воздухе (сжатие/вытягивание).
4.  **Fall (Падение):** Поза с вытянутыми руками/ногами.
5.  **Time Dilation (Замедление):** Наложение синего свечения на все спрайты при активации способности (эффект постобработки).

**Техническая реализация (Pygame):**

```python
class Player(pygame.sprite.Sprite):
    def __init__(self):
        self.animations = {
            'idle': self.load_frames('idle'),
            'walk': self.load_frames('walk'),
            'jump': self.load_frames('jump'),
        }
        self.current_anim = 'idle'
        self.frame_index = 0
        self.animation_speed = 0.15  # Задержка между кадрами

    def update_animation(self, state):
        # state = 'idle', 'walking', 'jumping'
        if state != self.current_anim:
            self.current_anim = state
            self.frame_index = 0

        # Продвижение анимации
        self.frame_index += self.animation_speed
        if self.frame_index >= len(self.animations[self.current_anim]):
            self.frame_index = 0

        self.image = self.animations[self.current_anim][int(self.frame_index)]
