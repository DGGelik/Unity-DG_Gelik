
# 📋 Урок 15: Массивы и Списки — Управляй Множеством Объектов

Привет, юный организатор арены! 💻 Добро пожаловать на второй уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **массивы** и **списки** в C#, чтобы управлять множеством объектов в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты создашь список врагов и систему управления бонусами, чтобы отслеживать их на арене. Готов организовать хаос? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BulletController`, `HealthBonus` из [Урок 14: Классы](../Advanced/Lesson14_Classes.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя база данных арены!  

**Предупреждение**: Списки требуют правильной работы с коллекциями и их обновления. Сохраняй код (**Ctrl+S**) перед тестом, иначе списки не обновятся! Если объекты не отслеживаются, проверь добавление и удаление из списков. Врубай Play Mode и управляй толпой!

Готов организовать арену? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Массивы и Списки?**
**Массивы** и **списки** в C# — это коллекции для хранения множества объектов:  
- **Массив**: Фиксированный размер, быстрый доступ (например, `GameObject[] enemies`).  
- **Список (List)**: Динамический размер, можно добавлять/удалять элементы (например, `List<GameObject> enemies`).  

В твоём шутере списки помогут отслеживать врагов и бонусы, чтобы управлять ими эффективно.

*Ссылка на документацию*: [C# Collections](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/collections).

## 🔄 **Зачем это Нужно?**
Списки упрощают управление множеством объектов, позволяя динамически добавлять и удалять их. В этом квесте ты:  
- Создашь список врагов для отслеживания.  
- Реализуешь систему управления бонусами.  
- Сделаешь арену организованной и управляемой!  

**Почему это круто?**  
- **Контроль**: Легко отслеживать все объекты.  
- **Гибкость**: Динамическое добавление/удаление.  
- **Для шутера**: Управление врагами и бонусами делает игру масштабной!

**Типичные ошибки новичков**:  
- Забыл инициализировать список (`new List<T>()`).  
- Неправильное удаление элементов из списка во время перебора.  
- Использование массива вместо списка для динамических данных.  

## ⚙️ **Квест: Управляй ареной**

### Уровень 1: Список врагов  
1. **Создай скрипт `GameManager`:**  
   - В **Project** создай **C# Script**, назови `GameManager`.  
   - Реализуй список врагов:  
     ```csharp
     using System.Collections.Generic;
     using UnityEngine;

     public class GameManager : MonoBehaviour
     {
         public static GameManager Instance { get; private set; }
         private List<Enemy> enemies = new List<Enemy>();

         void Awake()
         {
             if (Instance == null)
             {
                 Instance = this;
                 DontDestroyOnLoad(gameObject);
             }
             else
             {
                 Destroy(gameObject);
             }
         }

         public void AddEnemy(Enemy enemy)
         {
             enemies.Add(enemy);
             Debug.Log($"Добавлен враг: {enemy.name}, всего врагов: {enemies.Count}");
         }

         public void RemoveEnemy(Enemy enemy)
         {
             enemies.Remove(enemy);
             Debug.Log($"Удалён враг: {enemy.name}, осталось врагов: {enemies.Count}");
         }

         public List<Enemy> GetEnemies()
         {
             return enemies;
         }
     }
     ```

2. **Обнови `Enemy`:**  
   - Добавь регистрацию в `GameManager`:  
     ```csharp
     protected override void Start()
     {
         base.Start();
         GameManager.Instance.AddEnemy(this);
     }

     protected override void Die()
     {
         GameManager.Instance.RemoveEnemy(this);
         animator.SetTrigger("Die");
         Instantiate(explosionPrefab, transform.position, Quaternion.identity);
         AudioSource.PlayClipAtPoint(explosionSound, transform.position);
         Debug.Log($"{gameObject.name} уничтожен с взрывом!");
         Destroy(gameObject, 0.5f);
     }
     ```

3. **Полный код `Enemy`:**  
   ```csharp
   using UnityEngine;

   public abstract class Enemy : MonoBehaviour
   {
       [SerializeField] protected int maxHealth = 100;
       [SerializeField] protected float moveSpeed = 2.0f;
       [SerializeField] protected GameObject explosionPrefab;
       [SerializeField] protected AudioClip explosionSound;
       protected int currentHealth;
       protected Animator animator;

       protected virtual void Start()
       {
           currentHealth = maxHealth;
           animator = GetComponent<Animator>();
           GameManager.Instance.AddEnemy(this);
       }

       public virtual void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log($"{gameObject.name} получил урон! Осталось здоровья: {currentHealth}");
           if (currentHealth <= 0)
           {
               Die();
           }
       }

       protected virtual void Die()
       {
           GameManager.Instance.RemoveEnemy(this);
           animator.SetTrigger("Die");
           Instantiate(explosionPrefab, transform.position, Quaternion.identity);
           AudioSource.PlayClipAtPoint(explosionSound, transform.position);
           Debug.Log($"{gameObject.name} уничтожен с взрывом!");
           Destroy(gameObject, 0.5f);
       }
   }
   ```

4. **Настрой и протестируй:**  
   - В **Hierarchy** создай пустой объект `GameManager`, добавь компонент `GameManager`.  
   - Нажми **Play** — враги добавляются и удаляются из списка, в **Console** выводится их количество.  

### Уровень 2: Список бонусов  
1. **Обнови `GameManager`:**  
   - Добавь список бонусов:  
     ```csharp
     private List<HealthBonus> bonuses = new List<HealthBonus>();

     public void AddBonus(HealthBonus bonus)
     {
         bonuses.Add(bonus);
         Debug.Log($"Добавлен бонус: {bonus.name}, всего бонусов: {bonuses.Count}");
     }

     public void RemoveBonus(HealthBonus bonus)
     {
         bonuses.Remove(bonus);
         Debug.Log($"Удалён бонус: {bonus.name}, осталось бонусов: {bonuses.Count}");
     }
     ```

2. **Обнови `HealthBonus`:**  
   - Добавь регистрацию:  
     ```csharp
     void Start()
     {
         GameManager.Instance.AddBonus(this);
     }

     void OnTriggerEnter(Collider other)
     {
         if (other.CompareTag("Player"))
         {
             other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
             Instantiate(healEffectPrefab, transform.position, Quaternion.identity);
             AudioSource.PlayClipAtPoint(bonusSound, transform.position);
             Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
             GameManager.Instance.RemoveBonus(this);
             Destroy(gameObject);
         }
     }
     ```

3. **Полный код `HealthBonus`:**  
   ```csharp
   using System.Collections;
   using UnityEngine;

   public class HealthBonus : MonoBehaviour
   {
       [SerializeField] private int healthBoost = 25;
       [SerializeField] private GameObject healEffectPrefab;
       [SerializeField] private AudioClip bonusSound;

       void Start()
       {
           GameManager.Instance.AddBonus(this);
       }

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Player"))
           {
               other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
               Instantiate(healEffectPrefab, transform.position, Quaternion.identity);
               AudioSource.PlayClipAtPoint(bonusSound, transform.position);
               Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
               GameManager.Instance.RemoveBonus(this);
               Destroy(gameObject);
           }
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Убедись, что `HealthBonus.prefab` имеет правильные ссылки.  
   - Нажми **Play**, собери бонусы — они добавляются и удаляются из списка в **Console**.  

### Уровень 3: Отображение статистики  
1. **Добавь UI для статистики:**  
   - В **Hierarchy** в `GameUI` создай **UI > Text - TextMeshPro**, назови `StatsText`, позиция (x=0, y=100), текст: "Враги: 0, Бонусы: 0".  

2. **Обнови `GameManager`:**  
   - Добавь обновление UI:  
     ```csharp
     [SerializeField] private TMPro.TextMeshProUGUI statsText;

     void Update()
     {
         statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}";
     }
     ```

3. **Полный код `GameManager`:**  
   ```csharp
   using System.Collections.Generic;
   using UnityEngine;
   using TMPro;

   public class GameManager : MonoBehaviour
   {
       public static GameManager Instance { get; private set; }
       private List<Enemy> enemies = new List<Enemy>();
       private List<HealthBonus> bonuses = new List<HealthBonus>();
       [SerializeField] private TextMeshProUGUI statsText;

       void Awake()
       {
           if (Instance == null)
           {
               Instance = this;
               DontDestroyOnLoad(gameObject);
           }
           else
           {
               Destroy(gameObject);
           }
       }

       void Update()
       {
           statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}";
       }

       public void AddEnemy(Enemy enemy)
       {
           enemies.Add(enemy);
           Debug.Log($"Добавлен враг: {enemy.name}, всего врагов: {enemies.Count}");
       }

       public void RemoveEnemy(Enemy enemy)
       {
           enemies.Remove(enemy);
           Debug.Log($"Удалён враг: {enemy.name}, осталось врагов: {enemies.Count}");
       }

       public void AddBonus(HealthBonus bonus)
       {
           bonuses.Add(bonus);
           Debug.Log($"Добавлен бонус: {bonus.name}, всего бонусов: {bonuses.Count}");
       }

       public void RemoveBonus(HealthBonus bonus)
       {
           bonuses.Remove(bonus);
           Debug.Log($"Удалён бонус: {bonus.name}, осталось бонусов: {bonuses.Count}");
       }

       public List<Enemy> GetEnemies()
       {
           return enemies;
       }
   }
   ```

4. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `GameManager`, перетащи `StatsText` в поле `Stats Text`.  
   - Нажми **Play** — текст показывает количество врагов и бонусов в реальном времени!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поле `Stats Text` в `GameManager`.  
- В **Scene/Game**: Текст с количеством врагов и бонусов.  
- В **Console**: Сообщения о добавлении/удалении врагов и бонусов.  

## 💡 **Квесты: Прокачай управление!**
1. **Базовый квест**:  
   - В `StatsText` увеличь шрифт до 28 в **Inspector**.  
   - Проверь: статистика стала крупнее!  

2. **Квест на врагов**:  
   - В `GameManager` выведи имена всех врагов:  
     ```csharp
     foreach (var enemy in enemies)
     {
         Debug.Log($"Текущий враг: {enemy.name}");
     }
     ```  
   - Проверь: имена врагов в **Console**!  

3. **Квест на бонусы**:  
   - В `HealthBonus` добавь случайное здоровье:  
     ```csharp
     healthBoost = Random.Range(20, 50);
     ```  
   - Проверь: бонусы дают разное здоровье!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь в `GameManager` метод для уничтожения всех врагов:  
     ```csharp
     public void DestroyAllEnemies()
     {
         foreach (var enemy in enemies.ToArray())
         {
             enemy.TakeDamage(1000);
         }
     }
     ```  
   - В `PlayerController` вызови метод по клавише `Q`:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.Q))
     {
         GameManager.Instance.DestroyAllEnemies();
     }
     ```  
   - Проверь: нажатие `Q` уничтожает всех врагов!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если списки не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Инициализированы ли списки (`new List<T>()`)?  
  - Проверь `AddEnemy`/`RemoveEnemy` вызовы.  
- **Хочешь эпичности?** В `GameManager` добавь:  
  ```csharp
  Debug.Log($"Обновлено: Враги: {enemies.Count}, Бонусы: {bonuses.Count}");
  ```  
  - Увидишь статистику в **Console**!  
- **Список пуст?** Проверь регистрацию объектов в `Start`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Списки позволяют легко управлять множеством объектов, делая игру масштабируемой и организованной. Это ключ к сложным механикам!

## Заключение: Списки — Твой Органайзер Арены! 💻
Ты освоил списки, организовав врагов и бонусы. Твой шутер стал управляемым! Следующий шаг — оптимизация с Object Pooling. Продолжай, организатор кода!

**Что Далее?**  
- Перейди к [Object Pooling — Оптимизация для Пуль](../Advanced/Lesson16_Pooling.md) — ускорь игру.  
- Вопросы: [Unity Learn: Collections](https://learn.unity.com/tutorial/collections).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
