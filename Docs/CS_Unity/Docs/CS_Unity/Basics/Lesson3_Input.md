
# ⌨️ Урок 3: Ввод Игрока — Двигай Героя Клавишами!

Привет, юный воин арены! 💻 Добро пожаловать на третий уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером управления**, освоив **ввод игрока** в C#, чтобы твой герой в топ-даун шутере (*UnityTopDownShooterQuest*) бегал по арене с помощью клавиш WASD или стрелок. Ты создашь скрипт, который позволит герою двигаться, а заодно добавишь проверку здоровья, чтобы он "кричал" в консоль, если ранен. Готов взять управление в свои руки? Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Настроенный редактор кода ([Настройка Unity и Редактора Кода](../Preparation/Lesson2_SetupEditor.md)).  
- Скрипт `EnemyStats` из [Урок 2: Функции Start и Update](../Basics/Lesson2_Functions.md).  
- Папка Scripts в Assets — твоя лаборатория кодинга!  

**Предупреждение**: Ввод чувствителен к настройкам Unity — используй правильные названия клавиш (например, "Horizontal" для A/D). Сохраняй код (**Ctrl+S**) перед тестом, иначе герой не побежит! Если движения нет, проверь, прикреплён ли скрипт. Врубай Play Mode и стань пилотом своего героя!  

Готов управлять героем, как в настоящей игре? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Ввод Игрока?**
**Ввод игрока** — это система, которая ловит нажатия клавиш, мыши или геймпада и превращает их в действия в игре. В Unity за это отвечает **Input System** (старый, который мы используем в этом уроке) или **New Input System** (позже, в [Дополнительно: New Input System](../Additional/Lesson32_NewInput.md)). Ты будешь использовать функции `Input.GetAxis` для плавного движения героя по клавишам WASD или стрелкам.

В твоём шутере ввод позволит герою бегать по арене, уворачиваться от врагов и готовиться к стрельбе!

*Ссылка на документацию*: [Input](https://docs.unity3d.com/ScriptReference/Input.html).

## 🔄 **Зачем это Нужно?**
Ввод — это то, что делает игру интерактивной. Без него твой герой — просто статуя! В этом квесте ты:  
- Создашь скрипт для движения героя по клавишам.  
- Добавишь проверку здоровья, чтобы герой реагировал на урон.  
- Увидишь, как герой бегает по арене, а консоль сообщает о его состоянии.  

**Почему это круто?**  
- **Интерактивность**: Ты управляешь героем, как в настоящем шутере!  
- **Гибкость**: Настраивай скорость и направление через переменные.  
- **Для шутера**: Это основа для управления героем, чтобы он уклонялся от врагов и готовился к бою!  

**Типичные ошибки новичков**:  
- Неправильное имя оси ввода (например, "Horisontal" вместо "Horizontal").  
- Забыл умножить на `Time.deltaTime` — движение дерганое.  
- Скрипт не прикреплён к объекту героя.  

## ⚙️ **Квест: Управляй героем на арене**

### Уровень 1: Создай скрипт для героя  
1. **Открой проект.**  
   - Убедись, что проект готов (пройди [Создание Проекта](../../Preparation/CreateRun.md)).  

2. **Создай новый скрипт:**  
   - В **Project** найди папку `Assets`.  
   - Щёлкни правой кнопкой > **Create** > **C# Script**, назови `PlayerController`.  

3. **Добавь переменные и ввод:**  
   - Открой `PlayerController` в редакторе кода.  
   - Добавь переменные и код для движения:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class PlayerController : MonoBehaviour
     {
         [SerializeField] private float moveSpeed = 5.0f; // Скорость героя
         [SerializeField] private string playerName = "Hero"; // Имя героя
         [SerializeField] private int maxHealth = 100; // Максимальное здоровье
         private int currentHealth; // Текущее здоровье

         void Start()
         {
             currentHealth = maxHealth;
             Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
         }

         void Update()
         {
             // Получаем ввод с клавиш WASD/стрелок
             float moveX = Input.GetAxis("Horizontal"); // A/D или стрелки влево/вправо
             float moveY = Input.GetAxis("Vertical"); // W/S или стрелки вверх/вниз
             Vector3 movement = new Vector3(moveX, 0, moveY); // Вектор движения
             transform.Translate(movement * moveSpeed * Time.deltaTime);
         }
     }
     ```
   - `Input.GetAxis("Horizontal")` — возвращает значение от -1 (A/влево) до 1 (D/вправо).  
   - `Input.GetAxis("Vertical")` — от -1 (S/вниз) до 1 (W/вверх).  
   - `Vector3 movement` — комбинирует ввод по осям X и Z (для топ-даун шутера).  
   - `transform.Translate` — двигает героя с учётом скорости и `Time.deltaTime`.  

4. **Сохрани и прикрепи:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** создай **3D Object > Capsule**, назови `Player`.  
   - Перетащи `PlayerController` на `Player` (или **Add Component** > `PlayerController`).  
   - В **Inspector** проверь: `Move Speed` = 5, `Player Name` = Hero, `Max Health` = 100.  

5. **Протестируй движение:**  
   - Нажми **Play** (▶️).  
   - Используй клавиши **WASD** или **стрелки** — капсула (`Player`) движется в **Scene/Game**!  
   - В **Console** увидишь: "Hero готов к битве! Здоровье: 100, Скорость: 5".  

### Уровень 2: Добавь урон и реакцию  
1. **Создай метод для урона:**  
   - В `PlayerController` добавь:  
     ```csharp
     public void TakeDamage(int damage)
     {
         currentHealth -= damage;
         Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
         if (currentHealth <= 0)
         {
             Debug.Log(playerName + " повержен!");
             Destroy(gameObject);
         }
     }
     ```

2. **Тестируй урон в Start:**  
   - В `Start()` добавь:  
     ```csharp
     TakeDamage(30); // Тестовый урон
     ```
   - Полный код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class PlayerController : MonoBehaviour
     {
         [SerializeField] private float moveSpeed = 5.0f; // Скорость героя
         [SerializeField] private string playerName = "Hero"; // Имя героя
         [SerializeField] private int maxHealth = 100; // Максимальное здоровье
         private int currentHealth; // Текущее здоровье

         void Start()
         {
             currentHealth = maxHealth;
             Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
             TakeDamage(30);
         }

         void Update()
         {
             float moveX = Input.GetAxis("Horizontal");
             float moveY = Input.GetAxis("Vertical");
             Vector3 movement = new Vector3(moveX, 0, moveY);
             transform.Translate(movement * moveSpeed * Time.deltaTime);
         }

         public void TakeDamage(int damage)
         {
             currentHealth -= damage;
             Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
             if (currentHealth <= 0)
             {
                 Debug.Log(playerName + " повержен!");
                 Destroy(gameObject);
             }
         }
     }
     ```

3. **Проверь в Unity:**  
   - Сохрани (**Ctrl+S**).  
   - Нажми **Play**, двигай героя клавишами WASD/стрелки.  
   - В **Console**:  
     - "Hero готов к битве! Здоровье: 100, Скорость: 5"  
     - "Hero получил урон! Осталось здоровья: 70"  

### Уровень 3: Реакция на здоровье  
1. **Добавь проверку в Update:**  
   - В `Update()` добавь:  
     ```csharp
     if (currentHealth < 50)
     {
         Debug.Log(playerName + " ранен! Здоровье: " + currentHealth);
     }
     ```

2. **Полный код:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class PlayerController : MonoBehaviour
   {
       [SerializeField] private float moveSpeed = 5.0f; // Скорость героя
       [SerializeField] private string playerName = "Hero"; // Имя героя
       [SerializeField] private int maxHealth = 100; // Максимальное здоровье
       private int currentHealth; // Текущее здоровье

       void Start()
       {
           currentHealth = maxHealth;
           Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60); // Тестовый урон
       }

       void Update()
       {
           float moveX = Input.GetAxis("Horizontal");
           float moveY = Input.GetAxis("Vertical");
           Vector3 movement = new Vector3(moveX, 0, moveY);
           transform.Translate(movement * moveSpeed * Time.deltaTime);

           if (currentHealth < 50)
           {
               Debug.Log(playerName + " ранен! Здоровье: " + currentHealth);
           }
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
           if (currentHealth <= 0)
           {
               Debug.Log(playerName + " повержен!");
               Destroy(gameObject);
           }
       }
   }
   ```

3. **Протестируй:**  
   - Сохрани, нажми **Play**.  
   - Двигай героя WASD/стрелками, смотри в **Scene/Game**.  
   - В **Console**:  
     - "Hero готов к битве! Здоровье: 100, Скорость: 5"  
     - "Hero получил урон! Осталось здоровья: 40"  
     - Многократное "Hero ранен! Здоровье: 40" (каждый кадр).  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Move Speed`, `Player Name`, `Max Health` для `Player`.  
- В **Scene/Game**: Капсула (`Player`) движется по клавишам WASD/стрелки.  
- В **Console**: Сообщения о спавне, уроне и состоянии здоровья.  
- Если здоровье станет 0, капсула исчезнет с сообщением "повержен"!  

## 💡 **Квесты: Прокачай управление!**
1. **Базовый квест**:  
   - В **Inspector** измени `Move Speed` на 7.0, `Player Name` на "StarFighter", `Max Health` на 120.  
   - Нажми **Play**, проверь консоль: "StarFighter готов к битве! Здоровье: 120, Скорость: 7".  
   - Двигай героя — он быстрее!  

2. **Квест на урон**:  
   - Измени `TakeDamage(60)` в `Start` на `TakeDamage(120)`.  
   - Проверь, что герой уничтожается сразу (здоровье 120 - 120 = 0).  

3. **Квест с двумя героями**:  
   - Создай второй объект (**3D Object > Capsule**, назови `CoPilot`).  
   - Прикрепи `PlayerController`, в **Inspector** установи: `Move Speed` = 3.0, `Player Name` = "Sidekick", `Max Health` = 80.  
   - Нажми **Play**, управляй первым героем, а второй пусть пока стоит (или добавь другой ввод позже).  
   - Проверь консоль: сообщения от обоих героев!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь переменную для очков:  
     ```csharp
     [SerializeField] private int scoreValue = 100; // Очки за героя
     ```
   - В `TakeDamage`, при уничтожении, добавь:  
     ```csharp
     Debug.Log("Игра окончена! Набрано " + scoreValue + " очков за " + playerName + "!");
     ```
   - В `Update`, если здоровье < 50, замедли героя:  
     ```csharp
     if (currentHealth < 50)
     {
         Debug.Log(playerName + " ранен! Здоровье: " + currentHealth);
         moveSpeed = moveSpeed * 0.5f; // Половина скорости
     }
     ```
   - Полный код `Update`:  
     ```csharp
     void Update()
     {
         float moveX = Input.GetAxis("Horizontal");
         float moveY = Input.GetAxis("Vertical");
         Vector3 movement = new Vector3(moveX, 0, moveY);
         transform.Translate(movement * moveSpeed * Time.deltaTime);

         if (currentHealth < 50)
         {
             Debug.Log(playerName + " ранен! Здоровье: " + currentHealth);
             moveSpeed = moveSpeed * 0.5f;
         }
     }
     ```
   - В **Inspector** установи `Score Value` = 150 для `Player` и 50 для `CoPilot`.  
   - Проверь: при здоровье < 50 герой замедляется, при уничтожении — очки в консоли!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если герой не движется:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Прикреплён ли скрипт к капсуле?  
  - Проверь, правильно ли написаны "Horizontal" и "Vertical" в `Input.GetAxis`.  
- **Хочешь эпичности?** В `Update` добавь:  
  ```csharp
  Debug.Log("Позиция " + playerName + ": " + transform.position);
  ```
  - Увидишь координаты героя каждый кадр!  
- **Движение дерганое?** Убедись, что умножаешь на `Time.deltaTime`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Ввод игрока — сердце твоего шутера. Теперь твой герой может бегать по арене, а с проверкой здоровья ты готов к бою с врагами. Это основа для механик уклонения, стрельбы и сражений!

## Заключение: Ты Управляешь Ареной! 💻
Ты освоил ввод игрока, заставил героя двигаться и реагировать на урон. Твой шутер становится интерактивным! Следующий шаг — условные операторы, чтобы добавить мозги твоим механикам. Продолжай, воин арены!  

**Что Далее?**  
- Перейди к [Условные Операторы — Добавь Логику](./Lesson4_IfElse.md) — управляй поведением.  
- Вопросы: [Unity Learn: Input System](https://learn.unity.com/tutorial/introduction-to-input-system).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
