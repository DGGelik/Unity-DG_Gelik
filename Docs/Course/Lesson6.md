# 🕺 Урок 6: Анимация Боя — Стрельба, Движение и Взрывы Анимаций

Привет, аниматор экшена! 🕺 В этом уроке мы добавим **анимации для боя**: плавное движение героя (idle, walk), вспышку при стрельбе и взрывные эффекты (частицы) при попадании. Это сделает твой топ-даун шутер живым — герой не просто капсула, а динамичный боец! Используем Animator и бесплатные ассеты. Всё интегрируем в скрипты. Время: 25–40 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с GDD и прототипом из Урока 5.  
- Доступ к Asset Store (Window > Asset Store).  

**Предупреждение**: Анимации требуют импорт — если модель не загружается, перезагрузи Unity. Тестируй в Play Mode часто!  

Готов оживить перестрелку? По шагам, как по кадрам! 📝

## Шаг 1: Импортируй Модель с Анимациями — Базовый Герой с Движением 📥
Начнём с готовой модели, чтобы не рисовать с нуля.  

1. Открой **Asset Store** (Window > Asset Store). Ищи "free animated character" (например, "Third Person Controller Free" или "Simple Shooter Pack").  
2. Скачай и импортируй: Кликни "Download" > "Import All" (выбери базовые ассеты).  
3. В Project найди модель (обычно в папке Models). Перетащи в Hierarchy на место старой Capsule (замени героя).  
4. В Inspector модели:  
   - Rig: Animation Type = Generic (или Humanoid для продвинутого).  
   - Apply: Кликни, чтобы сохранить.  

**Готово!** Герой теперь анимированная модель с клипами (idle, walk).  
*Ссылка на помощь*: [Импорт Моделей с Анимацией](https://docs.unity3d.com/Manual/ImportingAnimations.html).  

## Шаг 2: Настрой Animator Controller — Состояния Анимации для Движения и Стрельбы 🎛️
Animator — "дирижёр" анимаций, переключает между idle, walk и shoot.  

1. В Project: Правой кнопкой на Animator Controllers > **Create > Animator Controller**. Назови "PlayerAnimator".  
2. Двойной клик на него — откроется Animator окно.  
3. Перетащи анимационные клипы из модели (в Project > Animations) в Animator: Idle, Walk, Shoot.  
4. Создай переходы:  
   - Idle → Walk: Правой кнопкой на Idle > Make Transition > Walk. Условие: Speed > 0.1 (параметр, создай: Parameters > Float "Speed").  
   - Walk → Idle: Speed < 0.1.  
   - Idle → Shoot: Trigger "Shoot" (Parameters > Trigger "Shoot").  
   - Shoot → Idle: Exit Time = 1.0 (авто-возврат).  
5. Прикрепи: Выбери героя в Hierarchy > В Animator компоненте перетащи "PlayerAnimator".  

**Готово!** Анимации переключаются по параметрам.  
*Ссылка на помощь*: [Animator Controller](https://docs.unity3d.com/Manual/AnimationOverview.html).  

## Шаг 3: Интегрируй Анимации со Скриптами — Триггеры для Стрельбы и Движения 🔗
Скрипты будут запускать анимации.  

1. Открой скрипт "PlayerMovement" (из Урока 3). Добавь в класс:  
   ```
   private Animator animator;  // Для анимаций

   void Start()
   {
       animator = GetComponent<Animator>();
       rb = GetComponent<Rigidbody>();
   }
   ```  
2. В Update() для движения:  
   ```
   float horizontal = Input.GetAxis("Horizontal");
   float vertical = Input.GetAxis("Vertical");
   float moveSpeed = Mathf.Sqrt(horizontal * horizontal + vertical * vertical);  // Общая скорость
   animator.SetFloat("Speed", moveSpeed);  // Триггер для walk/idle

   // ... (остальной код движения)
   ```  
3. Для стрельбы (в if Input.GetMouseButtonDown(0)):  
   ```
   animator.SetTrigger("Shoot");  // Запуск анимации shoot
   ```  
4. Сохрани скрипт.  

**Готово!** Движение и стрельба анимированы.  
*Ссылка на помощь*: [Скриптинг Анимаций](https://docs.unity3d.com/ScriptReference/Animator.SetTrigger.html).  

## Шаг 4: Добавь Взрывные Эффекты — Частицы для Попаданий 💥
Взрывы — это Particle System для визуального фидбека.  

1. Правой кнопкой в Hierarchy → **Effects > Particle System**. Назови "ExplosionEffect".  
2. В Inspector > Particle System:  
   - Duration: 0.5 сек.  
   - Start Lifetime: 1 сек.  
   - Start Speed: 5.  
   - Start Size: 0.5.  
   - Shape: Sphere (радиус 1).  
   - Renderer: Material — создай красный (как в Уроке 2).  
3. Позиционируй: Position на дуло героя (добавь пустой GameObject как child героя).  
4. В скрипте PlayerMovement (в if стрельбы):  
   ```
   Instantiate(explosionPrefab, firePoint.position, Quaternion.identity);  // firePoint — child объекта
   ```  
   (Создай prefab: Перетащи Particle в Project, назови "ExplosionPrefab").  
5. Сохрани и протестируй.  

**Готово!** Взрывы добавляют зрелища.  
*Ссылка на помощь*: [Particle System](https://docs.unity3d.com/Manual/ParticleSystems.html).  

## Шаг 4: Сохрани и Протестируй — Анимированный Бой ▶️
Проверим анимации на арене.  

1. Сохрани сцену и скрипты: **File > Save** (Ctrl+S).  
2. Кликни **Play**.  
3. В Game View: Двигай WASD — герой ходит/стоит анимировано. Кликай — стреляет с триггером и взрывом.  
4. Кликни **Stop**. Если анимация не играет — проверь Animator в Inspector.  

**Ура!** Бой анимирован — шутер оживает.  
*Ссылка на помощь*: [Тестирование Анимаций](https://docs.unity3d.com/Manual/AnimationPreview.html).  

## Что Далее? 🚀
- Перейди к [Уроку 7: Физика Перестрелки — Пули, Столкновения и Урон](./Lesson7.md) — добавим настоящие пули!  
- Вопросы: [Unity Learn: Animation Basics](https://learn.unity.com/tutorial/introduction-to-animation).  

Твои анимации — чистый адреналин! Продолжай, режиссёр. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*