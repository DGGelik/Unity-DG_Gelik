
# 🔍 Урок 36: LINQ в Unity — Умные Запросы к Данным

Привет, юный аналитик арены! 💻 Добро пожаловать на шестой уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **LINQ** (Language Integrated Query) в C# для работы с данными в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты будешь фильтровать врагов и бонусы, чтобы сделать игру умнее! Готов анализировать арену? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool`, `EnemyConfig`, `EnemyData`, `BonusData` из [Урок 35: Scriptable Objects](../Additional/Lesson35_ScriptableObjects.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `FastEnemy`, `HealthBonus`, `EnemyExplosion`, `BulletHitEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя аналитическая лаборатория!  

**Предупреждение**: LINQ требует правильной работы с коллекциями. Сохраняй код (**Ctrl+S**) перед тестом, иначе запросы не сработают! Если результаты LINQ неверные, проверь условия фильтрации. Врубай Play Mode и анализируй данные!

Готов сделать игру умнее? Погнали по уровням квеста! 🚀

## 🎯 **Что такое LINQ?**
**LINQ** — это инструмент C# для запросов к данным (списки, массивы).  
- **Where**: Фильтрует элементы по условию.  
- **Select**: Выбирает данные или преобразует их.  
- **OrderBy**: Сортирует элементы.  

В твоём шутере ты используешь LINQ для поиска ближайших врагов и бонусов.

*Ссылка на документацию*: [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/linq/).

## 🔄 **Зачем это Нужно?**
LINQ упрощает работу с данными, делая код чище и эффективнее. В этом квесте ты:  
- Оптимизируешь поиск ближайших врагов и бонусов.  
- Добавишь сортировку врагов по здоровью.  
- Сделаешь игру умной и быстрой!  

**Почему это круто?**  
- **Чистота кода**: Меньше циклов, больше читаемости.  
- **Эффективность**: Быстрые запросы к данным.  
- **Для шутера**: Умные враги и бонусы улучшают геймплей!  

**Типичные ошибки новичков**:  
- Неправильные условия в `Where`.  
- Использование LINQ в `Update` без кэширования.  
- Игнорирование производительности при больших коллекциях.  

## ⚙️ **Квест: Примени LINQ**

### Уровень 1: Поиск ближайшего врага  
1. **Обнови `PlayerController`:**  
   - Используй LINQ для поиска ближайшего врага:  
     ```csharp
     using System.Linq;

     private Enemy FindNearestEnemy()
     {
         var enemies = GameManager.Instance.GetEnemies();
         return enemies.OrderBy(e => Vector3.Distance(transform.position, e.transform.position)).FirstOrDefault();
     }

     void Update()
     {
         if (!isLocalPlayer)
         {
             healthBar.value = currentHealth;
             return;
         }

         Vector3 movement = new Vector3(moveInput.x, 0, moveInput.y);
         transform.Translate(movement * moveSpeed * Time.deltaTime);
         float speed = movement.magnitude;
         animator.SetFloat("Speed", speed);
         Debug.Log($"Игрок движется: {movement}, Скорость: {speed}");

         Enemy nearestEnemy = FindNearestEnemy();
         if (nearestEnemy != null)
         {
             Vector3 lookDirection = (nearestEnemy.transform.position - transform.position).normalized;
             transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
         }
         else
         {
             Ray ray = mainCamera.ScreenPointToRay(aimInput);
             RaycastHit hit;
             if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
             {
                 Vector3 lookDirection = (hit.point - transform.position).normalized;
                 transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
             }
         }

         if (currentHealth < 50)
         {
             moveSpeed = 2.0f;
             Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
         }
         else
         {
             moveSpeed = 5.0f;
         }

         CmdSyncTransform(transform.position, transform.rotation);
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play** — игрок смотрит на ближайшего врага, если он есть, иначе на курсор!  

### Уровень 2: Фильтрация бонусов  
1. **Обнови `NormalEnemy`:**  
   - Замени `FindNearestBonus` на LINQ:  
     ```csharp
     using System.Linq;

     private HealthBonus FindNearestBonus()
     {
         var bonuses = GameManager.Instance.GetBonuses();
         return bonuses.Where(b => Vector3.Distance(transform.position, b.transform.position) < bonusChaseDistance)
                       .OrderBy(b => Vector3.Distance(transform.position, b.transform.position))
                       .FirstOrDefault();
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play** — враги движутся только к бонусам в радиусе `bonusChaseDistance`!  

### Уровень 3: Сортировка врагов по здоровью  
1. **Обнови `GameManager`:**  
   - Добавь метод сортировки:  
     ```csharp
     using System.Linq;

     public void LogStrongestEnemies()
     {
         var strongEnemies = enemies.Where(e => e.GetHealth() > 50)
                                    .OrderByDescending(e => e.GetHealth())
                                    .Take(3);
         foreach (var enemy in strongEnemies)
         {
             Debug.Log($"Сильный враг: {enemy.name}, Здоровье: {enemy.GetHealth()}");
         }
     }
     ```

2. **Обнови `Enemy`:**  
   - Добавь метод:  
     ```csharp
     public int GetHealth() => currentHealth;
     ```

3. **Обнови `PlayerController`:**  
   - Вызови сортировку по клавише `L`:  
     ```csharp
     public void OnLogStrongest(InputAction.CallbackContext context)
     {
         if (context.performed)
         {
             GameManager.Instance.LogStrongestEnemies();
         }
     }
     ```

4. **Настрой `PlayerControls`:**  
   - Добавь действие `LogStrongest` (Button, Binding: L).  
   - В **Player Input** привяжи `OnLogStrongest`.  

5. **Настрой и протестируй:**  
   - Нажми **Play**, нажми `L` — в **Console** отобразятся топ-3 врага по здоровью!  

### Уровень 4: UI для ближайшего врага  
1. **Создай UI:**  
   - В **Hierarchy** добавь **TextMeshProUGUI**, назови `NearestEnemyText`.  
   - Установи текст: "Ближайший враг: Н/Д".  

2. **Обнови `PlayerController`:**  
   - Добавь отображение ближайшего врага:  
     ```csharp
     [SerializeField] private TMPro.TextMeshProUGUI nearestEnemyText;

     void Update()
     {
         if (!isLocalPlayer)
         {
             healthBar.value = currentHealth;
             return;
         }

         Vector3 movement = new Vector3(moveInput.x, 0, moveInput.y);
         transform.Translate(movement * moveSpeed * Time.deltaTime);
         float speed = movement.magnitude;
         animator.SetFloat("Speed", speed);
         Debug.Log($"Игрок движется: {movement}, Скорость: {speed}");

         Enemy nearestEnemy = FindNearestEnemy();
         if (nearestEnemy != null)
         {
             Vector3 lookDirection = (nearestEnemy.transform.position - transform.position).normalized;
             transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
             nearestEnemyText.text = $"Ближайший враг: {nearestEnemy.name} ({nearestEnemy.GetHealth()} HP)";
         }
         else
         {
             Ray ray = mainCamera.ScreenPointToRay(aimInput);
             RaycastHit hit;
             if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
             {
                 Vector3 lookDirection = (hit.point - transform.position).normalized;
                 transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
             }
             nearestEnemyText.text = "Ближайший враг: Н/Д";
         }
         // ... остальной код ...
     }
     ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Player`, перетащи `NearestEnemyText` в поле `Nearest Enemy Text`.  
   - Нажми **Play** — UI показывает ближайшего врага и его здоровье!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для действий и UI в `PlayerController`.  
- В **Scene/Game**: Игрок смотрит на ближайшего врага, враги движутся к ближайшим бонусам.  
- В **Console**: Логи сильнейших врагов.  
- В **Canvas**: UI с информацией о ближайшем враге.  

## 💡 **Квесты: Прокачай LINQ!**
1. **Базовый квест**:  
   - В `FindNearestEnemy` добавь фильтр по здоровью:  
     ```csharp
     return enemies.Where(e => e.GetHealth() > 0).OrderBy(e => Vector3.Distance(transform.position, e.transform.position)).FirstOrDefault();
     ```  
   - Проверь: игрок игнорирует мёртвых врагов!  

2. **Квест на бонусы**:  
   - В `FindNearestBonus` добавь фильтр по `healthBoost`:  
     ```csharp
     public int GetHealthBoost() => healthBoost;

     private HealthBonus FindNearestBonus()
     {
         var bonuses = GameManager.Instance.GetBonuses();
         return bonuses.Where(b => b.GetHealthBoost() > 30 && Vector3.Distance(transform.position, b.transform.position) < bonusChaseDistance)
                       .OrderBy(b => Vector3.Distance(transform.position, b.transform.position))
                       .FirstOrDefault();
     }
     ```  
   - Проверь: враги движутся к бонусам с большим лечением!  

3. **Квест на сортировку**:  
   - В `LogStrongestEnemies` добавь фильтр по боссам:  
     ```csharp
     var strongEnemies = enemies.Where(e => e is BossEnemy).OrderByDescending(e => e.GetHealth()).Take(3);
     ```  
   - Проверь: логируются только боссы!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь подсчёт врагов в радиусе:  
     - В `PlayerController`:  
       ```csharp
       public void LogEnemiesInRadius()
       {
           var enemies = GameManager.Instance.GetEnemies();
           float radius = 5.0f;
           var nearbyEnemies = enemies.Where(e => Vector3.Distance(transform.position, e.transform.position) < radius).ToList();
           nearestEnemyText.text = $"Врагов рядом: {nearbyEnemies.Count}";
           Debug.Log($"Врагов в радиусе {radius}: {nearbyEnemies.Count}");
       }

       public void OnLogEnemiesInRadius(InputAction.CallbackContext context)
       {
           if (context.performed)
           {
               LogEnemiesInRadius();
           }
       }
       ```  
     - В `PlayerControls` добавь действие `LogEnemiesInRadius` (Button, Binding: K).  
     - Проверь: UI и **Console** показывают количество врагов рядом!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если LINQ не работает:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь коллекции в `GameManager`.  
  - Используется ли `using System.Linq`?  
- **Хочешь эпичности?** В `LogStrongestEnemies` добавь:  
  ```csharp
  Debug.Log("Топ-3 сильнейших врагов арены!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Лаги от LINQ?** Кэшируй результаты в переменных, избегай LINQ в `Update`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
LINQ делает работу с данными быстрой и читаемой, улучшая ИИ и интерфейс. Это ключ к умной игре!

## Заключение: LINQ — Твой Аналитик Арены! 💻
Ты освоил LINQ, сделав игру умнее. Твой шутер стал аналитическим! Следующий шаг — лучшие практики для чистого кода. Продолжай, аналитик кода!

**Что Далее?**  
- Перейди к [Лучшие Практики — Пиши Чистый Код](../Additional/Lesson37_BestPractices.md) — улучши код.  
- Вопросы: [Unity Learn: C#](https://learn.unity.com/tutorial/coding-in-unity-for-the-absolute-beginner).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
