
# 🐛 Урок 31: Отладка — Лови Ошибки в Коде

Привет, юный охотник за багами! 💻 Добро пожаловать на первый уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **отладку** в Unity, чтобы находить и исправлять ошибки в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты научишься использовать **Debug.Log**, ** breakpoints** и **Profiler** для диагностики проблем! Готов выследить баги? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool` из [Урок 30: Сетевой Код](../Master/Lesson30_Networking.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя лаборатория отладки!  

**Предупреждение**: Отладка требует внимательности к логам и настройкам. Сохраняй код (**Ctrl+S**) перед тестом, иначе ошибки не отобразятся! Если баги не находятся, проверь **Console** и используй **Debugger**. Врубай Play Mode и лови баги!

Готов стать детективом кода? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Отладка?**
**Отладка** — это процесс поиска и исправления ошибок в коде.  
- **Debug.Log**: Логирует сообщения в **Console**.  
- **Breakpoints**: Точки останова в редакторе кода (например, Visual Studio).  
- **Profiler**: Анализирует производительность (CPU, GPU, память).  

В твоём шутере ты найдёшь и исправишь типичные ошибки (например, неправильное поведение врагов).

*Ссылка на документацию*: [Debugging](https://docs.unity3d.com/Manual/ScriptDebugging.html).

## 🔄 **Зачем это Нужно?**
Отладка помогает сделать игру стабильной и понятной. В этом квесте ты:  
- Используешь **Debug.Log** для отслеживания значений.  
- Поставишь точки останова для проверки логики.  
- Проверишь производительность с **Profiler**.  

**Почему это круто?**  
- **Стабильность**: Устранение багов.  
- **Понимание**: Прозрачность работы кода.  
- **Для шутера**: Отсутствие багов улучшает геймплей!  

**Типичные ошибки новичков**:  
- Игнорирование сообщений в **Console**.  
- Неправильная установка точек останова.  
- Не использование **Profiler** для оптимизации.  

## ⚙️ **Квест: Найди и исправь баги**

### Уровень 1: Логирование с Debug.Log  
1. **Обнови `PlayerController`:**  
   - Добавь логирование движения:  
     ```csharp
     void Update()
     {
         if (!isLocalPlayer) return;

         float moveX = Input.GetAxis("Horizontal");
         float moveY = Input.GetAxis("Vertical");
         Vector3 movement = new Vector3(moveX, 0, moveY);
         transform.Translate(movement * moveSpeed * Time.deltaTime);
         float speed = movement.magnitude;
         animator.SetFloat("Speed", speed);
         Debug.Log($"Игрок движется: {movement}, Скорость: {speed}");

         // ... остальной код ...
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, двигай игрока — в **Console** отобразятся данные о движении!  

### Уровень 2: Использование Breakpoints  
1. **Добавь баг в `NormalEnemy`:**  
   - Намеренно сделай ошибку в логике патрулирования:  
     ```csharp
     void Update()
     {
         if (player == null) return;

         bonusCheckTimer -= Time.deltaTime;
         HealthBonus nearestBonus = null;
         if (bonusCheckTimer <= 0)
         {
             nearestBonus = FindNearestBonus();
             bonusCheckTimer = bonusCheckInterval;
         }

         float distanceToPlayer = Vector3.Distance(transform.position, player.position);
         float distanceToBonus = nearestBonus != null ? Vector3.Distance(transform.position, nearestBonus.transform.position) : float.MaxValue;

         if (distanceToBonus < bonusChaseDistance)
         {
             Vector3 direction = (nearestBonus.transform.position - transform.position).normalized;
             transform.Translate(direction * moveSpeed * Time.deltaTime);
         }
         else if (distanceToPlayer < chaseDistance)
         {
             currentState = State.Chase;
             Vector3 direction = (player.position - transform.position).normalized;
             float wave = Mathf.Sin(Time.time * 2.0f) * 0.5f;
             Vector3 waveOffset = new Vector3(wave, 0, 0);
             transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
         }
         else
         {
             currentState = State.Patrol;
             Vector3 direction = (patrolPoint - transform.position).normalized;
             transform.Translate(direction * moveSpeed * Time.deltaTime);
             if (Vector3.Distance(transform.position, patrolPoint) < 0.5f)
             {
                 patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
                 Debug.Log($"Ошибка: Патрульная точка {patrolPoint} слишком близко!"); // Намеренный баг
             }
         }
     }
     ```

2. **Установи Breakpoint:**  
   - В Visual Studio (или другом редакторе) открой `NormalEnemy.cs`.  
   - Поставь точку останова на строке `Debug.Log($"Ошибка: ...")` (кликни слева от номера строки).  
   - Запусти игру в **Play Mode** с включённым дебаггером (кнопка **Attach to Unity**).  
   - Двигай врагов — дебаггер остановится, показывая значения `patrolPoint`.  

3. **Исправь баг:**  
   - Замени условие:  
     ```csharp
     if (Vector3.Distance(transform.position, patrolPoint) < 0.5f)
     {
         patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
         if (Vector3.Distance(transform.position, patrolPoint) < 1.0f) // Проверка на близость
         {
             patrolPoint += new Vector3(patrolRange, 0, patrolRange); // Корректировка
         }
         Debug.Log($"Новая патрульная точка: {patrolPoint}");
     }
     ```

4. **Настрой и протестируй:**  
   - Нажми **Play** — враги патрулируют корректно, без слишком близких точек!  

### Уровень 3: Анализ с Profiler  
1. **Проверь производительность:**  
   - Открой **Window** → **Analysis** → **Profiler**.  
   - Включи **Play Mode**, выбери врагов и стреляй — следи за пиками в **CPU Usage**.  

2. **Оптимизируй `EnemySpawner`:**  
   - Уменьши частоту спавна:  
     ```csharp
     [SerializeField] private float minSpawnInterval = 2.0f; // Было 1.0f
     ```

3. **Настрой и протестируй:**  
   - Нажми **Play**, проверь **Profiler** — меньше нагрузки на CPU!  

## 💻 **Что ты увидишь?**
- В **Console**: Логи движения игрока и патрулирования врагов.  
- В **Debugger**: Значения переменных на точках останова.  
- В **Profiler**: Уменьшение нагрузки после оптимизации.  

## 💡 **Квесты: Прокачай отладку!**
1. **Базовый квест**:  
   - В `PlayerController` добавь лог урона:  
     ```csharp
     Debug.Log($"Урон: {damage}, Осталось здоровья: {currentHealth}");
     ```  
   - Проверь: сообщения в **Console** при получении урона!  

2. **Квест на Breakpoint**:  
   - Поставь точку останова в `ShootWithReload` на строке `CmdShoot`.  
   - Проверь: дебаггер показывает направление стрельбы!  

3. **Квест на Profiler**:  
   - В **Profiler** включи **Deep Profile** и найди самый затратный метод в `NormalEnemy`.  
   - Проверь: оптимизируй его, уменьшив проверки!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь лог для событий пула:  
     - В `EventPool`:  
       ```csharp
       public void TriggerEvent(string eventName, object data = null)
       {
           if (eventDictionary.ContainsKey(eventName))
           {
               eventDictionary[eventName]?.Invoke(data);
               Debug.Log($"Событие {eventName} вызвано с данными: {data}");
           }
           else
           {
               Debug.LogWarning($"Событие {eventName} не найдено!");
           }
       }
       ```  
   - Проверь: **Console** показывает предупреждения о несуществующих событиях!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если баги не находятся:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь **Console** на ошибки.  
  - Используй **Profiler** для анализа нагрузки.  
- **Хочешь эпичности?** В `Debug.Log` добавь:  
  ```csharp
  Debug.LogError("Критический баг обнаружен!");
  ```  
  - Увидишь красное сообщение в **Console**!  
- **Дебаггер не останавливается?** Проверь подключение к Unity.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Отладка помогает находить и исправлять баги, делая игру стабильной. Это ключ к качеству!

## Заключение: Отладка — Твой Детектив Кода! 💻
Ты освоил отладку, выследив баги. Твой шутер стал стабильнее! Следующий шаг — современный ввод с New Input System. Продолжай, охотник за багами!

**Что Далее?**  
- Перейди к [New Input System — Современный Ввод](../Additional/Lesson32_NewInput.md) — улучши управление.  
- Вопросы: [Unity Learn: Debugging](https://learn.unity.com/tutorial/script-debugging).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
