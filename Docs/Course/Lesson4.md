# 🖥️ Урок 4: HUD Шутера — UI для Очков, Здоровья и Боеприпасов

Привет, интерфейсный гений! 🖥️ В этом уроке мы добавим **HUD (Heads-Up Display)** — экранный интерфейс для твоего топ-даун шутера: полоску здоровья, счётчик очков и индикатор боеприпасов. Это сделает игру читаемой, как в профессиональном аркадном экшене. UI будет обновляться динамически (через скрипты). Всё на Canvas — простом "полотне" для экрана. Время: 20–25 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с движущимся героем из Урока 3.  
- Базовый скрипт из Урока 3 (PlayerMovement).  

**Предупреждение**: UI элементы могут перекрывать сцену — используй Safe Area для мобильных (пока не нужно). Сохраняй скрипты перед тестом!  

Готов нарисовать экран битвы? По шагам, как по пикселям! 📝

## Шаг 1: Создай Canvas — Полотно для HUD 🖼️
Canvas — основа UI, как холст для картин.  

1. В **Hierarchy** правой кнопкой → **UI > Canvas**.  
2. Выбери Canvas в Hierarchy.  
3. В Inspector:  
   - Render Mode: Screen Space - Overlay (показывается поверх игры).  
   - Canvas Scaler: UI Scale Mode = Scale With Screen Size (адаптивно для экранов).  
   - Reference Resolution: X=1920, Y=1080 (стандарт HD).  

**Готово!** Canvas появился — пустой слой поверх сцены.  
*Ссылка на помощь*: [Введение в UI Canvas](https://docs.unity3d.com/Manual/UICanvas.html).  

## Шаг 2: Добавь UI Элементы — Здоровье, Очки и Боеприпасы 📊
Теперь разместим текст и полоски.  

1. Правой кнопкой на Canvas → **UI > Text - TextMeshPro** (если нет TMP — импортируй: Window > TextMeshPro > Import TMP Essentials). Назови "HealthText".  
2. В Inspector > Rect Transform:  
   - Anchor: Top-Left (верхний левый угол).  
   - Position: X=20, Y=-20 (отступ от края).  
3. В TextMeshPro компоненте:  
   - Text: "Health: 100/100".  
   - Font Size: 24, Color: Красный.  
4. Дублируй (правой кнопкой > Duplicate):  
   - "ScoreText": Text="Score: 0", Anchor: Top-Left, Position X=20, Y=-50, Color: Зелёный.  
   - "AmmoText": Text="Ammo: 30/30", Anchor: Top-Right, Position X=-20, Y=-20, Color: Синий.  
5. Для здоровья добавь полоску: Правой кнопкой на Canvas → **UI > Slider**. Назови "HealthBar".  
   - Anchor: Bottom-Left, Position X=20, Y=20.  
   - В Slider: Max Value=100, Value=100 (полная).  

**Готово!** HUD элементы на экране — статичные пока.  
*Ссылка на помощь*: [TextMeshPro и Slider](https://docs.unity3d.com/Packages/com.unity.textmeshpro@latest).  

## Шаг 3: Свяжи UI со Скриптом — Динамическое Обновление 📊
Скрипт будет менять числа при игре.  

1. Создай скрипт: В Project правой кнопкой → **Create > C# Script**. Назови "UIHealthManager".  
2. Двойной клик — открой и замени код:  
   ```
   using UnityEngine;
   using TMPro;  // Для TextMeshPro

   public class UIHealthManager : MonoBehaviour
   {
       public TextMeshProUGUI healthText;
       public TextMeshProUGUI scoreText;
       public TextMeshProUGUI ammoText;
       public Slider healthBar;

       private int currentHealth = 100;
       private int score = 0;
       private int ammo = 30;

       void Start()
       {
           UpdateUI();
       }

       public void UpdateHealth(int newHealth)
       {
           currentHealth = newHealth;
           UpdateUI();
       }

       public void AddScore(int points)
       {
           score += points;
           UpdateUI();
       }

       public void UpdateAmmo(int currentAmmo)
       {
           ammo = currentAmmo;
           UpdateUI();
       }

       void UpdateUI()
       {
           healthText.text = "Health: " + currentHealth + "/100";
           scoreText.text = "Score: " + score;
           ammoText.text = "Ammo: " + ammo + "/30";
           healthBar.value = currentHealth;
       }
   }
   ```  
3. Сохрани. В Hierarchy: Создай пустой GameObject (правой кнопкой > Create Empty), назови "UI Manager".  
4. Добавь скрипт: Выбери UI Manager → Add Component > UIHealthManager.  
5. В скрипте перетащи элементы: HealthText в healthText, ScoreText в scoreText и т.д. (Drag & Drop из Hierarchy).  

**Готово!** UI обновляется по вызову функций (позже свяжем с героем).  
*Ссылка на помощь*: [UI Scripting](https://docs.unity3d.com/Manual/UIScripting.html).  

## Шаг 4: Сохрани и Протестируй — HUD в Действии ▶️
Проверим интерфейс на арене.  

1. Сохрани сцену и скрипты: **File > Save** (Ctrl+S).  
2. Кликни **Play**.  
3. В Game View: Видишь текст в углах? Slider снизу? Двигай героя — HUD на месте.  
4. Для теста: В Console (Window > General > Console) выполни (если знаешь): Вызови UpdateHealth(50) — здоровье упадёт!  
5. Кликни **Stop**. Если текст не виден — проверь цвета и позиции.  

**Ура!** HUD живой — игрок видит статус.  
*Ссылка на помощь*: [Тестирование UI](https://docs.unity3d.com/Manual/UIElements.html).  

## Что Далее? 🚀
- Перейди к [Уроку 5: Концепция Шутера — GDD с Врагами и Механиками Стрельбы](./Lesson5.md) — спроектируем игру!  
- Вопросы: [Unity Learn: UI Basics](https://learn.unity.com/tutorial/create-a-user-interface).  

Твой HUD — как кокпит истребителя! Продолжай, стратег. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*