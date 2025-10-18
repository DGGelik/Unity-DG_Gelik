
# 🔄 Урок 2: Функции Start и Update — Оживи Игру!

Привет, юный мастер кода! 💻 Добро пожаловать во второй уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **заклинателем действий**, освоив **функции** `Start` и `Update` в C#, чтобы оживить твоего врага в топ-даун шутере (*UnityTopDownShooterQuest*). Функции — это как команды, которые говорят игре, что делать: спавнить врага, двигать его или проверять здоровье. Мы добавим движение врага по арене и заставим его "кричать" в консоль, когда он получает урон. Готов задать ритм битвы? Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Настроенный редактор кода ([Настройка Unity и Редактора Кода](../Preparation/Lesson2_SetupEditor.md)).  
- Скрипт `EnemyStats` из [Урок 1: Переменные](../Basics/Lesson1_Variables.md).  
- Папка Scripts в Assets — твоя мастерская магии!  

**Предупреждение**: Функции чувствительны к синтаксису — пиши точно (например, `void Start()` с большой буквы). Сохраняй код (**Ctrl+S**) перед тестом в Unity, иначе враг не оживёт! Если консоль молчит, проверь, прикреплён ли скрипт. Врубай Play Mode и смотри, как враг оживает на арене!  

Готов задать врагам движение? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Функции Start и Update?**
**Функции** в C# — это команды, которые выполняют действия, например, двигают врага или уменьшают здоровье. В Unity две ключевые функции:  
- **`Start()`**: Вызывается **один раз**, когда объект появляется в игре (идеально для настройки: задать здоровье, вывести приветствие).  
- **`Update()`**: Вызывается **каждый кадр** (много раз в секунду, как сердцебиение игры) — для движения, проверки условий или стрельбы.  

В твоём шутере `Start` будет настраивать врага (здоровье, имя), а `Update` — двигать его по арене, чтобы он гонялся за героем!

*Ссылка на документацию*: [MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html).

## 🔄 **Зачем это Нужно?**
Функции — это мотор твоей игры. Без них враги стоят на месте, а герой не стреляет. В этом квесте ты:  
- Используешь `Start` для начальной настройки врага.  
- Добавишь движение в `Update`, чтобы враг бегал по арене.  
- Создашь функцию для урона и проверишь её в действии.  

**Почему это круто?**  
- **Динамика**: `Update` делает врага живым, он движется каждый кадр!  
- **Контроль**: `Start` задаёт начальные параметры, чтобы всё работало как надо.  
- **Для шутера**: Это шаг к механике, где враги гоняются за тобой, а ты их разносишь пулями!  

**Типичные ошибки новичков**:  
- Забыл `void` перед функцией (`Start` не сработает без него).  
- Поставил код в `Start`, а нужно в `Update` (например, для движения).  
- Опечатка в имени функции (например, `start` вместо `Start`).  

## ⚙️ **Квест: Оживи врага с функциями**

### Уровень 1: Настрой врага в Start  
1. **Открой проект и скрипт.**  
   - Убедись, что у тебя есть проект и скрипт `EnemyStats` из [Урок 1: Переменные](../Basics/Lesson1_Variables.md).  
   - Открой `EnemyStats` в редакторе кода (дважды щёлкни в `Assets`).  

2. **Обнови скрипт с переменными и Start:**  
   - Убедись, что у тебя есть переменные и метод `TakeDamage` из прошлого урока.  
   - Полный стартовый код:  
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

         void Start()
         {
             currentHealth = maxHealth;
             Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
             TakeDamage(20); // Тестовый урон
         }

         void Update()
         {
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

3. **Проверь Start:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** убедись, что скрипт `EnemyStats` прикреплён к объекту `Enemy`.  
   - Нажми **Play** (▶️), открой **Console**.  
   - Увидишь:  
     - "CyberDrone готов к бою! Здоровье: 100, Скорость: 2.5"  
     - "CyberDrone получил урон! Осталось здоровья: 80"  

### Уровень 2: Движение в Update  
1. **Добавь движение врага:**  
   - Чтобы враг двигался вправо, добавь в `Update()`:  
     ```csharp
     transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
     ```
   - `transform.Translate` — двигает объект.  
   - `Vector3.right` — направление вправо (x=1).  
   - `moveSpeed` — скорость из переменной.  
   - `Time.deltaTime` — делает движение плавным, независимо от частоты кадров.  

2. **Обнови скрипт:**  
   - Полный код:  
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

         void Start()
         {
             currentHealth = maxHealth;
             Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
             TakeDamage(20); // Тестовый урон
         }

         void Update()
         {
             transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
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

3. **Добавь визуальный объект:**  
   - В **Hierarchy** щёлкни правой кнопкой > **3D Object** > **Cube**, назови `Enemy`.  
   - Перетащи скрипт `EnemyStats` на `Enemy` (или **Add Component** > `EnemyStats`).  
   - В **Scene** убедись, что куб виден (позиция, например, x=0, y=0, z=0).  

4. **Проверь движение:**  
   - Сохрани код (**Ctrl+S**).  
   - Нажми **Play** (▶️).  
   - Смотри в **Scene** или **Game** — куб (`Enemy`) движется вправо!  
   - В **Console** увидишь сообщения о спавне и уроне.  

### Уровень 3: Динамика с Update  
1. **Добавь проверку здоровья:**  
   - В `Update()` добавь:  
     ```csharp
     if (currentHealth < 50)
     {
         Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
     }
     ```
   - Это выводит сообщение каждый кадр, если здоровье ниже 50.  

2. **Полный код:**  
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

       void Start()
       {
           currentHealth = maxHealth;
           Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60); // Тестовый урон
       }

       void Update()
       {
           transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
           if (currentHealth < 50)
           {
               Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
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

3. **Протестируй:**  
   - Сохрани, нажми **Play**.  
   - В **Scene** куб движется вправо.  
   - В **Console**:  
     - "CyberDrone готов к бою! Здоровье: 100, Скорость: 2.5"  
     - "CyberDrone получил урон! Осталось здоровья: 40"  
     - Многократное "CyberDrone ослаб! Здоровье: 40" (каждый кадр).  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Max Health`, `Enemy Name`, `Move Speed` для `Enemy`.  
- В **Scene/Game**: Куб (`Enemy`) движется вправо с заданной скоростью.  
- В **Console**: Сообщения о спавне, уроне и слабом здоровье.  
- Если здоровье станет 0, куб исчезнет с сообщением "уничтожен"!  

## 💡 **Квесты: Прокачай арену!**
1. **Базовый квест**:  
   - В **Inspector** измени `Max Health` на 80, `Enemy Name` на "BattleBot", `Move Speed` на 4.0.  
   - Нажми **Play**, проверь консоль: "BattleBot готов к бою! Здоровье: 80, Скорость: 4.0".  
   - Убедись, что куб движется быстрее!  

2. **Квест на урон**:  
   - Измени `TakeDamage(60)` в `Start` на `TakeDamage(80)`.  
   - Проверь, что враг уничтожается сразу (здоровье 80 - 80 = 0).  

3. **Квест с двумя врагами**:  
   - Создай второй объект (**3D Object > Cube**, назови `MiniEnemy`).  
   - Прикрепи `EnemyStats`, в **Inspector** установи: `Max Health` = 50, `Enemy Name` = "MiniBot", `Move Speed` = 3.0.  
   - Нажми **Play**, убедись, что оба врага движутся и выводят свои сообщения в консоль!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь переменную для очков:  
     ```csharp
     [SerializeField] private int scoreValue = 20; // Очки за убийство
     ```
   - В `TakeDamage`, при уничтожении, добавь:  
     ```csharp
     Debug.Log("Ты заработал " + scoreValue + " очков за " + enemyName + "!");
     ```
   - В `Update`, если здоровье < 50, замедли врага:  
     ```csharp
     if (currentHealth < 50)
     {
         Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
         moveSpeed = moveSpeed * 0.5f; // Половина скорости
     }
     ```
   - Полный код `Update`:  
     ```csharp
     void Update()
     {
         transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
         if (currentHealth < 50)
         {
             Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
             moveSpeed = moveSpeed * 0.5f;
         }
     }
     ```
   - В **Inspector** установи `Score Value` = 30 для `Enemy` и 15 для `MiniEnemy`.  
   - Проверь: при здоровье < 50 враг замедляется, при уничтожении — очки в консоли!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если враг не движется или консоль пустая:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Прикреплён ли скрипт к кубу?  
  - Проверь ошибки в консоли — может, опечатка в `Start` или `Update`.  
- **Хочешь эпичности?** В `Update` добавь:  
  ```csharp
  Debug.Log("Позиция " + enemyName + ": " + transform.position);
  ```
  - Увидишь координаты врага каждый кадр!  
- **Слишком быстро?** Уменьши `moveSpeed` в Inspector до 1.0 для плавности.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Функции `Start` и `Update` — мотор твоих механик. `Start` настраивает врагов, а `Update` делает их живыми: движение, атака, проверка здоровья. Ты только что создал врага, который бегает и теряет здоровье — основа для эпичных сражений на арене!

## Заключение: Функции — Твой Двигатель Битвы! 💻
Ты освоил `Start` и `Update`, заставил врага двигаться и реагировать на урон. Теперь твой шутер оживает! Следующий шаг — ввод игрока, чтобы твой герой бегал по клавишам. Продолжай, воин кода!  

**Что Далее?**  
- Перейди к [Ввод Игрока — Двигай Героя Клавишами](./Lesson3_Input.md) — управляй героем.  
- Вопросы: [Unity Learn: Scripting Basics](https://learn.unity.com/tutorial/introduction-to-scripting-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*