
# ❓ Урок 4: Условные Операторы — Добавь Логику в Твой Шутер!

Привет, юный стратег арены! 💻 Добро пожаловать на четвёртый уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером логики**, освоив **условные операторы** (`if`, `else`, `else if`) в C#, чтобы твой герой и враги в топ-даун шутере (*UnityTopDownShooterQuest*) действовали умно. Ты научишь героя замедляться при низком здоровье и менять цвет, а врага — атаковать только вблизи. Готов добавить мозги своей игре? Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController` и `EnemyStats` из [Урок 3: Ввод Игрока](../Basics/Lesson3_Input.md).  
- Настроенный редактор кода ([Настройка Unity и Редактора Кода](../Preparation/Lesson2_SetupEditor.md)).  
- Папка Scripts в Assets — твоя мастерская кода!  

**Предупреждение**: Условия требуют точности — проверяй скобки `{}` и знаки сравнения (`==`, `<`, `>`). Сохраняй код (**Ctrl+S**) перед тестом, иначе логика не сработает! Если герой или враг ведут себя странно, проверь консоль на ошибки. Врубай Play Mode и смотри, как твоя арена оживает!  

Готов сделать игру умнее? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Условные Операторы?**
**Условные операторы** (`if`, `else`, `else if`) — это команды, которые проверяют условия и решают, что делать. Например: "Если здоровье героя < 50, замедли его!" или "Если враг близко, атакуй!". Они как развилки в квесте: выбирают путь в зависимости от ситуации.  

В твоём шутере ты используешь условия, чтобы:  
- Замедлить героя, когда он ранен.  
- Поменять цвет врага при низком здоровье.  
- Активировать атаку врага только рядом с героем.  

*Ссылка на документацию*: [C# Control Flow](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/statements/selection-statements).

## 🔄 **Зачем это Нужно?**
Условия — это мозги твоей игры. Без них герой и враги действуют одинаково, а с ними — реагируют на события: здоровье, расстояние, действия игрока. В этом квесте ты:  
- Научишь героя замедляться при низком здоровье.  
- Заставишь врага менять цвет, когда он ранен.  
- Добавишь логику атаки для врага.  

**Почему это круто?**  
- **Умные механики**: Герой и враги реагируют на ситуацию, как в настоящем шутере!  
- **Интерактивность**: Логика делает игру живой и интересной.  
- **Для шутера**: Это шаг к системе боя, где враги атакуют с умом, а герой адаптируется к урону!  

**Типичные ошибки новичков**:  
- Забыл `==` в сравнении (пиши `health == 0`, а не `health = 0`).  
- Неправильные скобки или отступы — код не сработает.  
- Поставил условие в `Start` вместо `Update` (логика должна проверяться каждый кадр).  

## ⚙️ **Квест: Добавь логику в игру**

### Уровень 1: Логика для героя  
1. **Открой скрипт `PlayerController`.**  
   - Убедись, что у тебя есть скрипт из [Урок 3: Ввод Игрока](../Basics/Lesson3_Input.md).  
   - Открой `PlayerController` в редакторе кода.  

2. **Добавь условие в Update:**  
   - В `Update`, после движения, добавь проверку здоровья:  
     ```csharp
     if (currentHealth < 50)
     {
         moveSpeed = 2.0f; // Замедляем героя
         Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
     }
     else
     {
         moveSpeed = 5.0f; // Обычная скорость
     }
     ```

3. **Полный код `PlayerController`:**  
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
           TakeDamage(60);
       }

       void Update()
       {
           float moveX = Input.GetAxis("Horizontal");
           float moveY = Input.GetAxis("Vertical");
           Vector3 movement = new Vector3(moveX, 0, moveY);
           transform.Translate(movement * moveSpeed * Time.deltaTime);

           if (currentHealth < 50)
           {
               moveSpeed = 2.0f;
               Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
           }
           else
           {
               moveSpeed = 5.0f;
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

4. **Протестируй героя:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `PlayerController` прикреплён к объекту `Player` (капсула).  
   - Нажми **Play** (▶️), двигай героя WASD/стрелками.  
   - В **Console**:  
     - "Hero готов к битве! Здоровье: 100, Скорость: 5"  
     - "Hero получил урон! Осталось здоровья: 40"  
     - Многократное "Hero ранен и замедлен! Скорость: 2"  
   - В **Scene/Game**: Герой движется медленнее при здоровье < 50.  

### Уровень 2: Логика для врага  
1. **Открой скрипт `EnemyStats`.**  
   - Используй скрипт из [Урок 2: Функции Start и Update](../Basics/Lesson2_Functions.md).  

2. **Добавь изменение цвета при уроне:**  
   - Добавь переменную для рендера и условие в `Update`:  
     ```csharp
     private Renderer enemyRenderer; // Для изменения цвета

     void Start()
     {
         currentHealth = maxHealth;
         enemyRenderer = GetComponent<Renderer>(); // Получаем компонент рендера
         Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
         TakeDamage(60);
     }

     void Update()
     {
         transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
         if (currentHealth < 50)
         {
             enemyRenderer.material.color = Color.red; // Красный при низком здоровье
             Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
             moveSpeed = moveSpeed * 0.5f;
         }
         else
         {
             enemyRenderer.material.color = Color.white; // Обычный цвет
         }
     }
     ```

3. **Полный код `EnemyStats`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemyStats : MonoBehaviour
   {
       [SerializeField] private int maxHealth = 100; // Максимальное здоровье
       [SerializeField] private string enemyName = "CyberDrone"; // Имя врага
       [SerializeField] private float moveSpeed = 2.5f; // Скорость движения
       private int currentHealth; // Текущее здоровье
       private Renderer enemyRenderer; // Для изменения цвета

       void Start()
       {
           currentHealth = maxHealth;
           enemyRenderer = GetComponent<Renderer>();
           Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60);
       }

       void Update()
       {
           transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
           if (currentHealth < 50)
           {
               enemyRenderer.material.color = Color.red;
               Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
               moveSpeed = moveSpeed * 0.5f;
           }
           else
           {
               enemyRenderer.material.color = Color.white;
           }
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log(enemyName + " получил урон! Осталось здоровья: " + currentHealth);
           if (currentHealth <= 0)
           {
               Debug.Log(enemyName + " уничтожен!");
               Destroy(gameObject);
           }
       }
   }
   ```

4. **Протестируй врага:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `EnemyStats` прикреплён к кубу (`Enemy`).  
   - Нажми **Play**, смотри **Scene/Game**: куб становится красным при здоровье < 50.  
   - В **Console**: сообщения о спавне, уроне и ослаблении.  

### Уровень 3: Логика атаки врага  
1. **Добавь проверку расстояния до героя:**  
   - В `EnemyStats` добавь переменную и метод атаки:  
     ```csharp
     [SerializeField] private GameObject player; // Ссылка на героя
     [SerializeField] private float attackDistance = 3.0f; // Дистанция атаки

     void Update()
     {
         transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
         if (currentHealth < 50)
         {
             enemyRenderer.material.color = Color.red;
             Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
             moveSpeed = moveSpeed * 0.5f;
         }
         else
         {
             enemyRenderer.material.color = Color.white;
         }

         float distanceToPlayer = Vector3.Distance(transform.position, player.transform.position);
         if (distanceToPlayer < attackDistance)
         {
             Debug.Log(enemyName + " атакует " + player.GetComponent<PlayerController>().playerName + "!");
         }
     }
     ```

2. **Полный код `EnemyStats`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemyStats : MonoBehaviour
   {
       [SerializeField] private int maxHealth = 100; // Максимальное здоровье
       [SerializeField] private string enemyName = "CyberDrone"; // Имя врага
       [SerializeField] private float moveSpeed = 2.5f; // Скорость движения
       [SerializeField] private GameObject player; // Ссылка на героя
       [SerializeField] private float attackDistance = 3.0f; // Дистанция атаки
       private int currentHealth; // Текущее здоровье
       private Renderer enemyRenderer; // Для изменения цвета

       void Start()
       {
           currentHealth = maxHealth;
           enemyRenderer = GetComponent<Renderer>();
           Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60);
       }

       void Update()
       {
           transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
           if (currentHealth < 50)
           {
               enemyRenderer.material.color = Color.red;
               Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
               moveSpeed = moveSpeed * 0.5f;
           }
           else
           {
               enemyRenderer.material.color = Color.white;
           }

           float distanceToPlayer = Vector3.Distance(transform.position, player.transform.position);
           if (distanceToPlayer < attackDistance)
           {
               Debug.Log(enemyName + " атакует " + player.GetComponent<PlayerController>().playerName + "!");
           }
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log(enemyName + " получил урон! Осталось здоровья: " + currentHealth);
           if (currentHealth <= 0)
           {
               Debug.Log(enemyName + " уничтожен!");
               Destroy(gameObject);
           }
       }
   }
   ```

3. **Настрой связь с героем:**  
   - В **Hierarchy** выбери объект `Enemy`.  
   - В **Inspector** перетащи объект `Player` (капсулу) в поле `Player` скрипта `EnemyStats`.  
   - Нажми **Play**, двигай героя ближе/дальше от врага (в пределах 3 единиц).  
   - В **Console**: сообщения об атаке, если герой близко!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для героя и врага (`Move Speed`, `Max Health`, `Player`).  
- В **Scene/Game**: Герой движется медленнее при здоровье < 50, враг становится красным и замедляется.  
- В **Console**: Сообщения о состоянии, уроне и атаках врага.  
- Если здоровье = 0, объекты исчезают!  

## 💡 **Квесты: Прокачай логику!**
1. **Базовый квест**:  
   - В **Inspector** для `Player` установи `Max Health` = 80, `Player Name` = "StarFighter".  
   - Для `Enemy` установи `Max Health` = 60, `Enemy Name` = "BattleBot".  
   - Нажми **Play**, проверь консоль и убедись, что герой замедляется, а враг краснеет.  

2. **Квест на настройку атаки**:  
   - Измени `attackDistance` в **Inspector** для `Enemy` на 5.0.  
   - Двигай героя ближе/дальше, проверь, что атака срабатывает на большей дистанции.  

3. **Квест с двумя врагами**:  
   - Создай второй куб (`MiniEnemy`), прикрепи `EnemyStats`.  
   - Установи: `Max Health` = 40, `Enemy Name` = "MiniBot", `Move Speed` = 3.0, `Attack Distance` = 2.0.  
   - Перетащи `Player` в поле `Player` для `MiniEnemy`.  
   - Нажми **Play**, проверь, что оба врага атакуют, если герой близко!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `PlayerController` добавь очки за выживание:  
     ```csharp
     [SerializeField] private int survivalPoints = 10; // Очки за кадр выживания
     private int totalScore = 0; // Общий счёт
     ```
   - В `Update` добавь:  
     ```csharp
     if (currentHealth > 0)
     {
         totalScore += survivalPoints;
         Debug.Log(playerName + " выживает! Очки: " + totalScore);
     }
     ```
   - В `EnemyStats` добавь урон герою при атаке:  
     ```csharp
     float distanceToPlayer = Vector3.Distance(transform.position, player.transform.position);
     if (distanceToPlayer < attackDistance)
     {
         Debug.Log(enemyName + " атакует " + player.GetComponent<PlayerController>().playerName + "!");
         player.GetComponent<PlayerController>().TakeDamage(10);
     }
     ```
   - В **Inspector** установи `Survival Points` = 5 для `Player`.  
   - Нажми **Play**: герой теряет здоровье, если враг близко, и зарабатывает очки за выживание!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если логика не работает:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Прикреплён ли `Player` к полю `Player` в `EnemyStats`?  
  - Проверь ошибки в консоли — опечатка в `if` или имени переменной?  
- **Хочешь эпичности?** В `EnemyStats` добавь:  
  ```csharp
  Debug.Log("Расстояние до игрока: " + Vector3.Distance(transform.position, player.transform.position));
  ```
  - Увидишь расстояние до героя каждый кадр!  
- **Слишком много сообщений?** Убери `Debug.Log` из `Update`, если консоль заспамлена.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Условные операторы делают твою игру умной: герой реагирует на урон, враги атакуют с умом. Это основа для системы боя, где враги будут преследовать героя, а он — адаптироваться к ситуации!

## Заключение: Логика — Твой Командир Арены! 💻
Ты добавил мозги своему шутеру: герой замедляется, враги меняют цвет и атакуют с умом. Следующий шаг — циклы, чтобы автоматизировать действия, например, стрельбу! Продолжай, стратег кода!  

**Что Далее?**  
- Перейди к [Циклы — Повторяй Действия для Стрельбы](./Lesson5_Loops.md) — автоматизируй действия.  
- Вопросы: [Unity Learn: Scripting Logic](https://learn.unity.com/tutorial/introduction-to-scripting-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
