
# 📦 Урок 1: Переменные — Храни Здоровье и Скорость для Шутера!

Привет, юный герой кодинга! 💻 Добро пожаловать в первый уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **хранителем данных**, освоив **переменные** в C# — как ящики с сокровищами, где лежат здоровье врагов, скорость героя или очки для твоего топ-даун шутера (*UnityTopDownShooterQuest*). Мы создадим скрипт, чтобы управлять здоровьем врага, скоростью и даже его "кличкой", а ты увидишь результат в консоли, как настоящий мастер арены! Это твой старт к созданию эпичных механик. Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (пройди [Создание Проекта](../../Preparation/CreateRun.md)).  
- Настроенный редактор кода ([Настройка Unity и Редактора Кода](../Preparation/Lesson2_SetupEditor.md)).  
- Папка Scripts в Assets — твоя кузница кода!  

**Предупреждение**: Переменные — как оружие, их имена чувствительны к регистру (health ≠ Health). Сохраняй код (**Ctrl+S**) перед тестом в Unity, иначе магия не сработает! Если консоль молчит, проверь, прикреплён ли скрипт к объекту. Врубай Play Mode и смотри результат сразу, как в настоящей игре!  

Готов ворваться в код и задать врагам жару? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Переменные?**
**Переменные** — это твои сундуки для хранения данных в игре. Хочешь знать, сколько здоровья у врага? Сколько патронов у героя? Как быстро бежит босс? Переменные держат эти числа, тексты или другие штуки! В C# каждая переменная имеет **тип**:  
- `int` — целые числа (здоровье = 100).  
- `float` — дробные числа (скорость = 2.5).  
- `string` — текст (имя врага = "Кибердрон").  

В твоём шутере переменные — это сердце механик: здоровье врагов, скорость игрока, очки за победу. Сегодня ты создашь врага, который "расскажет" о своём здоровье и скорости в консоли!

*Ссылка на документацию*: [C# Variables](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/variables).

## 🔄 **Зачем это Нужно?**
Переменные — как инвентарь в RPG: без них ты не сможешь отслеживать здоровье врагов, скорость героя или очки. В этом квесте ты:  
- Создашь скрипт с переменными для врага (здоровье, имя, скорость).  
- Научишься менять их в Inspector и коде.  
- Увидишь, как данные оживают в консоли.  

**Почему это круто?**  
- **Гибкость**: Меняй здоровье врага — и босс станет слабее или сильнее!  
- **Контроль**: Переменные делают код понятным, без "магических чисел" (например, 100).  
- **Для шутера**: Это первый шаг к системе урона, где враги теряют здоровье от твоих пуль!  

**Типичные ошибки новичков**:  
- Неправильный тип (например, `int` вместо `float` для скорости).  
- Забыл дать переменной начальное значение (например, `int health;` без `= 100`).  
- Опечатка в имени переменной — Unity не простит!  

## ⚙️ **Квест: Создаём скрипт с переменными**

### Уровень 1: Создай базу для врага  
1. **Открой Unity и свой проект.**  
   Убедись, что проект готов (пройди [Создание Проекта](../../Preparation/CreateRun.md)).  

2. **Создай новый скрипт:**  
   - В окне **Project** найди папку `Assets`.  
   - Щёлкни правой кнопкой > **Create** > **C# Script**, назови `EnemyStats`.  
   - Имя файла и класса **должны совпадать**, иначе Unity выдаст ошибку!  

3. **Открой скрипт:**  
   - Дважды щёлкни по `EnemyStats` в `Assets` — откроется твой редактор кода (Visual Studio или VS Code).  
   - Ты увидишь шаблон:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class EnemyStats : MonoBehaviour
     {
         // Start is called before the first frame update
         void Start()
         {
             
         }

         // Update is called once per frame
         void Update()
         {
             
         }
     }
     ```

4. **Добавь переменные для врага:**  
   - Перед `Start()`, внутри класса, добавь:  
     ```csharp
     [SerializeField] private int maxHealth = 100; // Максимальное здоровье
     [SerializeField] private string enemyName = "CyberDrone"; // Имя врага
     [SerializeField] private float moveSpeed = 2.5f; // Скорость движения
     private int currentHealth; // Текущее здоровье (скрыто от Inspector)
     ```
   - `[SerializeField]` — делает переменные видимыми в Inspector, чтобы ты мог менять их без кода.  
   - `private int currentHealth;` — хранит текущее здоровье, не видно в Inspector.  

5. **Инициализируй и выведи данные:**  
   - В `Start()` добавь:  
     ```csharp
     currentHealth = maxHealth; // Устанавливаем текущее здоровье
     Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
     ```
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
         private int currentHealth; // Текущее здоровье (скрыто от Inspector)

         void Start()
         {
             currentHealth = maxHealth;
             Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
         }

         void Update()
         {
         }
     }
     ```

6. **Сохрани код:**  
   - Нажми **Ctrl+S** и вернись в Unity — скрипт обновится.  

7. **Прикрепи скрипт к врагу:**  
   - В **Hierarchy** щёлкни правой кнопкой > **Create Empty**, назови `Enemy`.  
   - Перетащи `EnemyStats` на `Enemy` (или в **Inspector** выбери **Add Component** > `EnemyStats`).  
   - В **Inspector** увидишь поля: `Max Health` (100), `Enemy Name` (CyberDrone), `Move Speed` (2.5).  

8. **Запусти и проверь:**  
   - Нажми **Play** (▶️) и открой **Console** (Window > General > Console).  
   - Увидишь: **"CyberDrone готов к бою! Здоровье: 100, Скорость: 2.5"**. Твои переменные ожили! 🎉  

*Ссылка на документацию*: [SerializeField](https://docs.unity3d.com/ScriptReference/SerializeField.html).

### Уровень 2: Добавь механику урона  
1. **Создай метод для урона:**  
   - В скрипте `EnemyStats` после `Update()` добавь:  
     ```csharp
     public void TakeDamage(int damage)
     {
         currentHealth -= damage;
         Debug.Log(enemyName + " получил урон! Осталось здоровья: " + currentHealth);
         if (currentHealth <= 0)
         {
             Debug.Log(enemyName + " уничтожен!");
             Destroy(gameObject); // Удаляем врага
         }
     }
     ```
   - Этот метод уменьшает здоровье и уничтожает врага, если здоровье ≤ 0.  

2. **Протестируй урон:**  
   - В `Start()` добавь тестовый урон:  
     ```csharp
     TakeDamage(20); // Наносим 20 урона при спавне
     ```
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

3. **Проверь в Unity:**  
   - Сохрани код (**Ctrl+S**).  
   - Нажми **Play** и смотри консоль.  
   - Увидишь:  
     - "CyberDrone готов к бою! Здоровье: 100, Скорость: 2.5"  
     - "CyberDrone получил урон! Осталось здоровья: 80"  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Max Health`, `Enemy Name`, `Move Speed` для объекта `Enemy`.  
- В **Console**: Сообщения о спавне и уроне.  
- Если здоровье станет 0 (попробуй `TakeDamage(100)`), враг исчезнет с сообщением "уничтожен"!  

## 💡 **Квесты: Прокачай свои навыки!**
1. **Базовый квест**:  
   - В **Inspector** измени `Max Health` на 50, `Enemy Name` на "BossKiller", `Move Speed` на 3.5.  
   - Нажми **Play** и проверь консоль: "BossKiller готов к бою! Здоровье: 50, Скорость: 3.5".  

2. **Квест на урон**:  
   - Измени `TakeDamage(20)` в `Start()` на `TakeDamage(50)`.  
   - Проверь, что враг уничтожается сразу (здоровье 50 - 50 = 0).  

3. **Квест с новым врагом**:  
   - Создай новый объект в **Hierarchy**, назови `MiniDrone`.  
   - Прикрепи к нему `EnemyStats`.  
   - В **Inspector** установи: `Max Health` = 30, `Enemy Name` = "MiniDrone", `Move Speed` = 4.0.  
   - Нажми **Play** и убедись, что консоль показывает данные обоих врагов!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь новую переменную:  
     ```csharp
     [SerializeField] private int scoreValue = 10; // Очки за убийство
     ```
   - В методе `TakeDamage`, при уничтожении врага, выведи:  
     ```csharp
     Debug.Log("Получено " + scoreValue + " очков за " + enemyName + "!");
     ```
   - Полный метод `TakeDamage`:  
     ```csharp
     public void TakeDamage(int damage)
     {
         currentHealth -= damage;
         Debug.Log(enemyName + " получил урон! Осталось здоровья: " + currentHealth);
         if (currentHealth <= 0)
         {
             Debug.Log(enemyName + " уничтожен!");
             Debug.Log("Получено " + scoreValue + " очков за " + enemyName + "!");
             Destroy(gameObject);
         }
     }
     ```
   - В **Inspector** установи `Score Value` = 25 для `Enemy` и 15 для `MiniDrone`.  
   - Проверь консоль: при уничтожении врагов должны отображаться очки!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если консоль пустая:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Прикреплён ли скрипт к объекту `Enemy`?  
  - Проверь красные ошибки в консоли — опечатка в имени переменной или класса?  
- **Хочешь эпичности?** Попробуй в `Update()` уменьшать здоровье:  
  ```csharp
  currentHealth -= 1;
  Debug.Log("Здоровье падает: " + currentHealth);
  ```
  - Увидишь, как здоровье уменьшается каждый кадр, пока враг не исчезнет!  
- **Не работает в Inspector?** Убедись, что переменные помечены `[SerializeField]`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Переменные — как патроны в обойме: без них не будет ни здоровья врагов, ни скорости героя, ни очков за победу. Ты только что создал основу для системы здоровья врагов — в будущем пули будут вызывать `TakeDamage`, и враги будут падать на арене! Этот квест готовит тебя к созданию механик урона и подсчёта очков.

## Заключение: Переменные — Твой Инвентарь для Шутера! 💻
Ты освоил переменные, создал врага с здоровьем и скоростью, а главное — увидел, как они работают в игре! Теперь ты готов к следующему уровню: функции, чтобы твои враги двигались и атаковали. Продолжай, герой кода!  

**Что Далее?**  
- Перейди к [Функции Start и Update — Оживи Игру](./Lesson2_Functions.md) — оживи механики.  
- Вопросы: [Unity Learn: Scripting Variables](https://learn.unity.com/tutorial/introduction-to-scripting-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*