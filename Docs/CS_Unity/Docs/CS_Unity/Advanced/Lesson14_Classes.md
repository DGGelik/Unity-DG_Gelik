
# 🏗️ Урок 14: Классы и Наследование — Шаблоны для Врагов

Привет, юный архитектор кода! 💻 Добро пожаловать на первый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером классов**, освоив **наследование** в C# для создания шаблонов врагов в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Наследование позволит создать общий класс для врагов и разные их типы, такие как обычные враги и боссы, с уникальным поведением. Готов построить армию врагов? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus`, `CrosshairController`, `HealthBarController` из [Урок 13: Звук](../Intermediate/Lesson13_Audio.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus`, `HitEffect`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя мастерская классов!  

**Предупреждение**: Наследование требует правильной структуры классов и полиморфизма. Сохраняй код (**Ctrl+S**) перед тестом, иначе поведение врагов не обновится! Если враги ведут себя некорректно, проверь ссылки на префабы и методы в скриптах. Врубай Play Mode и создай армию врагов!

Готов строить шаблоны? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Классы и Наследование?**
**Классы** в C# — это шаблоны для объектов, содержащие данные и поведение. **Наследование** позволяет создавать дочерние классы, которые наследуют свойства и методы родительского класса, добавляя или изменяя функционал.  
- **Базовый класс**: Общий шаблон (например, `Enemy`).  
- **Дочерний класс**: Специфический тип (например, `Boss`).  
- **override**: Переопределение методов для уникального поведения.  

В твоём шутере ты создашь базовый класс `Enemy` и два типа врагов: обычный и босс с увеличенным здоровьем и скоростью.

*Ссылка на документацию*: [C# Classes](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/).

## 🔄 **Зачем это Нужно?**
Наследование упрощает управление разными типами врагов, позволяя переиспользовать код. В этом квесте ты:  
- Создашь базовый класс `Enemy` с общим поведением.  
- Реализуешь классы `NormalEnemy` и `BossEnemy` с уникальными характеристиками.  
- Обновишь `EnemySpawner` для спавна разных врагов.  

**Почему это круто?**  
- **Гибкость**: Легко добавлять новые типы врагов.  
- **Переиспользование**: Общий код для всех врагов.  
- **Для шутера**: Разнообразие врагов делает игру интереснее!

**Типичные ошибки новичков**:  
- Забыл использовать `virtual`/`override` для методов.  
- Неправильные ссылки на дочерние классы в префабах.  
- Неправильная иерархия классов.  

## ⚙️ **Квест: Построй армию врагов**

### Уровень 1: Создай базовый класс `Enemy`  
1. **Создай скрипт `Enemy`:**  
   - В **Project** создай **C# Script**, назови `Enemy`.  
   - Замени `EnemyStats` на новый базовый класс:  
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
             animator.SetTrigger("Die");
             Instantiate(explosionPrefab, transform.position, Quaternion.identity);
             AudioSource.PlayClipAtPoint(explosionSound, transform.position);
             Debug.Log($"{gameObject.name} уничтожен с взрывом!");
             Destroy(gameObject, 0.5f);
         }
     }
     ```

2. **Создай класс `NormalEnemy`:**  
   - Создай **C# Script**, назови `NormalEnemy`.  
   - Реализуй наследование:  
     ```csharp
     using UnityEngine;

     public class NormalEnemy : Enemy
     {
         [SerializeField] private Transform player;

         protected override void Start()
         {
             base.Start();
             player = GameObject.FindGameObjectWithTag("Player").transform;
         }

         void Update()
         {
             if (player != null)
             {
                 Vector3 direction = (player.position - transform.position).normalized;
                 transform.Translate(direction * moveSpeed * Time.deltaTime);
             }
         }
     }
     ```

3. **Создай класс `BossEnemy`:**  
   - Создай **C# Script**, назови `BossEnemy`.  
   - Реализуй уникальное поведение:  
     ```csharp
     using UnityEngine;

     public class BossEnemy : Enemy
     {
         [SerializeField] private Transform player;
         [SerializeField] private int damage = 20;

         protected override void Start()
         {
             base.Start();
             maxHealth = 300; // Босс сильнее
             moveSpeed = 3.0f; // Босс быстрее
             currentHealth = maxHealth;
             player = GameObject.FindGameObjectWithTag("Player").transform;
         }

         void Update()
         {
             if (player != null)
             {
                 Vector3 direction = (player.position - transform.position).normalized;
                 transform.Translate(direction * moveSpeed * Time.deltaTime);
             }
         }

         void OnCollisionEnter(Collision other)
         {
             if (other.gameObject.CompareTag("Player"))
             {
                 other.gameObject.GetComponent<PlayerController>().TakeDamage(damage);
                 Debug.Log($"{gameObject.name} нанёс урон игроку!");
             }
         }
     }
     ```

4. **Обнови префабы:**  
   - Открой `Enemy.prefab`, замени компонент `EnemyStats` на `NormalEnemy`.  
   - Создай новый префаб `Boss` (скопируй `Enemy.prefab`, назови `Boss.prefab`).  
   - В `Boss.prefab` замени `NormalEnemy` на `BossEnemy`, добавь тег `Boss`.  
   - Настрой `Boss.prefab`: масштаб (x=2, y=2, z=2), материал (например, красный).  

### Уровень 2: Обнови `EnemySpawner`  
1. **Обнови скрипт `EnemySpawner`:**  
   - Добавь поддержку боссов:  
     ```csharp
     [SerializeField] private GameObject normalEnemyPrefab;
     [SerializeField] private GameObject bossEnemyPrefab;
     [SerializeField] private float bossSpawnChance = 0.1f; // 10% шанс на босса
     ```

   - Измени `SpawnEnemy`:  
     ```csharp
     private void SpawnEnemy()
     {
         GameObject prefabToSpawn = Random.value < bossSpawnChance ? bossEnemyPrefab : normalEnemyPrefab;
         Vector3 spawnPosition = new Vector3(
             Random.Range(-spawnRange, spawnRange),
             0,
             Random.Range(-spawnRange, spawnRange)
         );
         Instantiate(prefabToSpawn, spawnPosition, Quaternion.identity);
         Debug.Log($"Создан враг: {prefabToSpawn.name} на позиции {spawnPosition}");
     }
     ```

2. **Полный код `EnemySpawner`:**  
   ```csharp
   using System.Collections;
   using UnityEngine;

   public class EnemySpawner : MonoBehaviour
   {
       [SerializeField] private GameObject normalEnemyPrefab;
       [SerializeField] private GameObject bossEnemyPrefab;
       [SerializeField] private float spawnRange = 10.0f;
       [SerializeField] private float spawnInterval = 2.0f;
       [SerializeField] private float bossSpawnChance = 0.1f;

       void Start()
       {
           StartCoroutine(SpawnRoutine());
       }

       private IEnumerator SpawnRoutine()
       {
           while (true)
           {
               SpawnEnemy();
               yield return new WaitForSeconds(spawnInterval);
           }
       }

       private void SpawnEnemy()
       {
           GameObject prefabToSpawn = Random.value < bossSpawnChance ? bossEnemyPrefab : normalEnemyPrefab;
           Vector3 spawnPosition = new Vector3(
               Random.Range(-spawnRange, spawnRange),
               0,
               Random.Range(-spawnRange, spawnRange)
           );
           Instantiate(prefabToSpawn, spawnPosition, Quaternion.identity);
           Debug.Log($"Создан враг: {prefabToSpawn.name} на позиции {spawnPosition}");
       }
   }
   ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `EnemySpawner`, перетащи `Enemy.prefab` в `Normal Enemy Prefab`, а `Boss.prefab` в `Boss Enemy Prefab`.  
   - Нажми **Play** — появляются обычные враги и иногда боссы, которые быстрее и наносят урон при столкновении!  

### Уровень 3: Обнови `BulletController`  
1. **Обнови `BulletController` для работы с `Enemy`:**  
   - Измени `TakeDamage`:  
     ```csharp
     if (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss"))
     {
         hit.collider.GetComponent<Enemy>().TakeDamage(damage);
         Instantiate(sparkEffectPrefab, hit.point, Quaternion.identity);
         Debug.Log("Пуля попала в " + hit.collider.name + " с искрами!");
         Destroy(gameObject);
     }
     ```
   - Аналогично в `OnTriggerEnter`:  
     ```csharp
     if (other.CompareTag("Enemy") || other.CompareTag("Boss"))
     {
         other.GetComponent<Enemy>().TakeDamage(damage);
         Instantiate(sparkEffectPrefab, transform.position, Quaternion.identity);
         Debug.Log("Пуля попала в " + other.name + " с искрами!");
         Destroy(gameObject);
     }
     ```

2. **Полный код `BulletController`:**  
   ```csharp
   using System.Collections;
   using UnityEngine;

   public class BulletController : MonoBehaviour
   {
       [SerializeField] private int damage = 20;
       [SerializeField] private GameObject sparkEffectPrefab;

       void Start()
       {
           Destroy(gameObject, 3.0f);
       }

       void Update()
       {
           Ray ray = new Ray(transform.position, transform.GetComponent<Rigidbody>().velocity.normalized);
           RaycastHit hit;
           if (Physics.Raycast(ray, out hit, 0.5f, LayerMask.GetMask("EnemyLayer", "BossLayer")))
           {
               if (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss"))
               {
                   hit.collider.GetComponent<Enemy>().TakeDamage(damage);
                   Instantiate(sparkEffectPrefab, hit.point, Quaternion.identity);
                   Debug.Log("Пуля попала в " + hit.collider.name + " с искрами!");
                   Destroy(gameObject);
               }
           }
       }

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Enemy") || other.CompareTag("Boss"))
           {
               other.GetComponent<Enemy>().TakeDamage(damage);
               Instantiate(sparkEffectPrefab, transform.position, Quaternion.identity);
               Debug.Log("Пуля попала в " + other.name + " с искрами!");
               Destroy(gameObject);
           }
       }
   }
   ```

3. **Настрой и протестируй:**  
   - Убедись, что `Enemy.prefab` и `Boss.prefab` имеют правильные теги и компоненты.  
   - Нажми **Play**, стреляй — пули наносят урон обоим типам врагов!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для `Normal Enemy Prefab`, `Boss Enemy Prefab`, настройки здоровья и скорости.  
- В **Scene/Game**: Обычные враги и боссы двигаются к игроку, боссы наносят урон при столкновении.  
- В **Console**: Сообщения о создании врагов, уроне и уничтожении.  

## 💡 **Квесты: Прокачай врагов!**
1. **Базовый квест**:  
   - В `BossEnemy` увеличь `maxHealth` до 500 в **Inspector**.  
   - Проверь: боссы стали ещё прочнее!  

2. **Квест на движение**:  
   - В `NormalEnemy` уменьши `moveSpeed` до 1.0 в **Inspector**.  
   - Проверь: обычные враги двигаются медленнее!  

3. **Квест на боссов**:  
   - В `EnemySpawner` увеличь `bossSpawnChance` до 0.2.  
   - Проверь: боссы появляются чаще!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Создай новый класс `FastEnemy`, наследующий `Enemy`:  
     ```csharp
     public class FastEnemy : Enemy
     {
         [SerializeField] private Transform player;

         protected override void Start()
         {
             base.Start();
             moveSpeed = 4.0f; // Быстрый враг
             player = GameObject.FindGameObjectWithTag("Player").transform;
         }

         void Update()
         {
             if (player != null)
             {
                 Vector3 direction = (player.position - transform.position).normalized;
                 transform.Translate(direction * moveSpeed * Time.deltaTime);
             }
         }
     }
     ```  
   - Создай префаб `FastEnemy.prefab`, добавь `FastEnemy`, настрой масштаб (x=0.8, y=0.8, z=0.8).  
   - В `EnemySpawner` добавь поле `[SerializeField] private GameObject fastEnemyPrefab;` и измени `SpawnEnemy`:  
     ```csharp
     GameObject prefabToSpawn;
     float rand = Random.value;
     if (rand < bossSpawnChance)
         prefabToSpawn = bossEnemyPrefab;
     else if (rand < 0.3f)
         prefabToSpawn = fastEnemyPrefab;
     else
         prefabToSpawn = normalEnemyPrefab;
     ```  
   - Проверь: появляются быстрые враги!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если враги не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь теги и компоненты в префабах.  
  - Использованы ли `virtual`/`override`?  
- **Хочешь эпичности?** В `BossEnemy` добавь:  
  ```csharp
  Debug.Log($"{gameObject.name} атакует игрока!");
  ```  
  - Увидишь атаки босса в **Console**!  
- **Враги не спавнятся?** Проверь ссылки на префабы в `EnemySpawner`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Наследование позволяет легко создавать новых врагов с уникальным поведением, сохраняя общий код. Это ключ к разнообразию и масштабируемости игры!

## Заключение: Классы — Твой Шаблон Армии! 💻
Ты освоил наследование, создав шаблоны для врагов. Твой шутер стал разнообразнее с боссами! Следующий шаг — массивы и списки для управления множеством объектов. Продолжай, архитектор кода!

**Что Далее?**  
- Перейди к [Массивы и Списки — Управляй Множеством Объектов](../Advanced/Lesson15_Lists.md) — работай с коллекциями.  
- Вопросы: [Unity Learn: C#](https://learn.unity.com/tutorial/c-programming).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
