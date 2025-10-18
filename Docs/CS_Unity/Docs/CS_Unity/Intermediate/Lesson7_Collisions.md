
# 💥 Урок 7: Коллайдеры — Столкновения и Урон!

Привет, юный мастер боя! 💻 Добро пожаловать на первый уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **королём столкновений**, освоив **коллайдеры** в Unity, чтобы пули в твоём топ-даун шутере (*UnityTopDownShooterQuest*) наносили урон врагам, а герой собирал бонусы. Коллайдеры — это невидимые зоны, которые определяют, когда объекты "сталкиваются". Ты улучшишь систему боя и добавишь бонус здоровья! Готов взорвать арену? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner` из [Урок 6: Компоненты GameObject](../Basics/Lesson6_Components.md).  
- Префабы `Bullet` и `Enemy` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя боевая лаборатория!  

**Предупреждение**: Коллайдеры требуют точной настройки (`Is Trigger`, теги). Сохраняй код (**Ctrl+S**) перед тестом, иначе столкновения не сработают! Если пули проходят сквозь врагов, проверь теги и компоненты. Врубай Play Mode и разноси врагов!

Готов зарядить столкновения? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Коллайдеры?**
**Коллайдеры** — это компоненты Unity, которые задают "зону столкновений" для объектов. Они определяют, когда пуля попала во врага или герой подхватил бонус.  
- **Box/Sphere/Capsule Collider**: Форма зоны (куб, сфера, капсула).  
- **Is Trigger**: Включает события столкновений без физики (для урона).  
- **Rigidbody**: Нужен для физических столкновений.  

В твоём шутере коллайдеры сделают пули смертельными, а бонусы — полезными!

*Ссылка на документацию*: [Коллайдеры](https://docs.unity3d.com/ScriptReference/Collider.html).

## 🔄 **Зачем это Нужно?**
Коллайдеры — сердце боевой системы. Они позволяют пулям наносить урон, а герою собирать бонусы. В этом квесте ты:  
- Улучшишь систему попаданий пуль.  
- Добавишь бонус здоровья для героя.  
- Увидишь, как столкновения делают игру живой!

**Почему это круто?**  
- **Реализм**: Пули бьют врагов, бонусы лечат героя!  
- **Интерактивность**: Столкновения оживляют игру.  
- **Для шутера**: Это основа для системы боя и бонусов!

**Типичные ошибки новичков**:  
- Забыл включить `Is Trigger` для событий столкновений.  
- Отсутствует `Rigidbody` или неправильные теги.  
- Коллайдеры слишком маленькие/большие, из-за чего попадания не срабатывают.

## ⚙️ **Квест: Заряди арену столкновениями**

### Уровень 1: Улучши столкновения пуль  
1. **Проверь префаб пули:**  
   - Открой `Bullet.prefab` в `Assets/Prefabs`.  
   - Убедись, что есть **Sphere Collider** (`Is Trigger` включён) и **Rigidbody** (`Use Gravity` выключен).  

2. **Создай скрипт для пули:**  
   - В **Project** создай **C# Script**, назови `BulletController`.  
   - Добавь код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class BulletController : MonoBehaviour
     {
         [SerializeField] private int damage = 20; // Урон пули

         void OnTriggerEnter(Collider other)
         {
             if (other.CompareTag("Enemy"))
             {
                 other.GetComponent<EnemyStats>().TakeDamage(damage);
                 Debug.Log("Пуля попала в " + other.name + "!");
                 Destroy(gameObject);
             }
         }
     }
     ```

3. **Настрой пулю:**  
   - Открой `Bullet.prefab`, добавь **BulletController** (**Add Component**).  
   - Убедись, что `Sphere Collider` имеет `Is Trigger` включённым.  
   - Сохрани префаб.  

4. **Протестируй столкновения:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `Enemy.prefab` имеет тег `Enemy` (**Tag > Enemy**).  
   - Нажми **Play**, стреляй (**Space**) по врагам — они получают урон и исчезают при здоровье 0!  
   - В **Console**: сообщения о попаданиях.  

### Уровень 2: Добавь бонус здоровья  
1. **Создай префаб бонуса:**  
   - В **Hierarchy** создай **3D Object > Cube**, назови `HealthBonus`.  
   - Установи масштаб: x=0.5, y=0.5, z=0.5.  
   - Добавь **Box Collider** (`Is Trigger` включён).  
   - Щёлкни правой кнопкой > **Create Prefab**, сохрани как `Assets/Prefabs/HealthBonus.prefab`.  
   - Удали `HealthBonus` из **Hierarchy**.  

2. **Создай скрипт для бонуса:**  
   - В **Project** создай **C# Script**, назови `HealthBonus`.  
   - Добавь код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class HealthBonus : MonoBehaviour
     {
         [SerializeField] private int healthBoost = 25; // Восстановление здоровья

         void OnTriggerEnter(Collider other)
         {
             if (other.CompareTag("Player"))
             {
                 other.GetComponent<PlayerController>().TakeDamage(-healthBoost); // Отрицательный урон = лечение
                 Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
                 Destroy(gameObject);
             }
         }
     }
     ```

3. **Настрой бонус:**  
   - Открой `HealthBonus.prefab`, добавь **HealthBonus** скрипт.  
   - В **Hierarchy** установи тег `Player` для объекта `Player` (**Tag > Player**).  
   - Создай бонус: щёлкни правой кнопкой в **Hierarchy** > **Create > Prefab > HealthBonus**, позиция (x=2, y=0, z=2).  
   - Нажми **Play**, двигай героя к бонусу — он исчезает, здоровье увеличивается!  

### Уровень 3: Спавн бонусов со спавнером  
1. **Обнови `EnemySpawner`:**  
   - Добавь спавн бонуса:  
     ```csharp
     [SerializeField] private GameObject bonusPrefab; // Префаб бонуса
     ```

2. **Измени `Start`:**  
   - В `EnemySpawner` замени `Start` на:  
     ```csharp
     void Start()
     {
         int spawnedEnemies = 0;
         while (spawnedEnemies < enemiesToSpawn)
         {
             Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
             Debug.Log("Спавн врага " + (spawnedEnemies + 1));
             spawnedEnemies++;
         }
         Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
         Debug.Log("Спавн бонуса здоровья!");
     }
     ```

3. **Полный код `EnemySpawner`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemySpawner : MonoBehaviour
   {
       [SerializeField] private GameObject enemyPrefab;
       [SerializeField] private GameObject bonusPrefab;
       [SerializeField] private int enemiesToSpawn = 5;
       [SerializeField] private float spawnDelay = 1.0f;

       void Start()
       {
           int spawnedEnemies = 0;
           while (spawnedEnemies < enemiesToSpawn)
           {
               Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
               Debug.Log("Спавн врага " + (spawnedEnemies + 1));
               spawnedEnemies++;
           }
           Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
           Debug.Log("Спавн бонуса здоровья!");
       }
   }
   ```

4. **Протестируй спавн:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** выбери `Spawner`, перетащи `HealthBonus.prefab` в поле `Bonus Prefab` в **Inspector**.  
   - Нажми **Play**: враги спавнятся, затем появляется бонус здоровья в (0, 0, 5). Собери его героем!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для урона пули, здоровья бонуса и настроек спавнера.  
- В **Scene/Game**: Пули бьют врагов, бонусы лечат героя.  
- В **Console**: Сообщения о попаданиях, лечении и спавне.  

## 💡 **Квесты: Прокачай арену!**
1. **Базовый квест**:  
   - В **Inspector** для `Bullet.prefab` установи `Damage` = 30.  
   - Для `HealthBonus.prefab` установи `Health Boost` = 50.  
   - Нажми **Play**, стреляй по врагам, собирай бонус — враги уничтожаются быстрее, здоровье восстанавливается больше!  

2. **Квест на столкновения**:  
   - В `HealthBonus.prefab` увеличь размер `Box Collider` до x=1, y=1, z=1.  
   - Проверь: бонус легче собрать!  

3. **Квест с несколькими бонусами**:  
   - В `EnemySpawner` добавь второй бонус в (0, 0, -5):  
     ```csharp
     Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
     Instantiate(bonusPrefab, new Vector3(0, 0, -5), Quaternion.identity);
     Debug.Log("Спавн двух бонусов здоровья!");
     ```  
   - Проверь: спавнятся два бонуса, оба можно собрать!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `BulletController` добавь случайный урон:  
     ```csharp
     [SerializeField] private int minDamage = 15;
     [SerializeField] private int maxDamage = 25;

     void OnTriggerEnter(Collider other)
     {
         if (other.CompareTag("Enemy"))
         {
             int randomDamage = Random.Range(minDamage, maxDamage + 1);
             other.GetComponent<EnemyStats>().TakeDamage(randomDamage);
             Debug.Log("Пуля попала в " + other.name + " на " + randomDamage + " урона!");
             Destroy(gameObject);
         }
     }
     ```  
   - В **Inspector** установи `Min Damage` = 10, `Max Damage` = 30.  
   - Проверь: пули наносят случайный урон, отображается в **Console**!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если столкновения не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Правильные ли теги (`Player`, `Enemy`)?  
  - Включён ли `Is Trigger` на коллайдерах?  
- **Хочешь эпичности?** В `HealthBonus` добавь:  
  ```csharp
  Debug.Log("Бонус на позиции: " + transform.position);
  ```  
  - Увидишь позиции бонусов в **Console**!  
- **Нет столкновений?** Проверь размеры коллайдеров и настройки `Rigidbody`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Коллайдеры делают боевую систему реальной: пули наносят урон, бонусы лечат. Это основа для эпичных сражений и наград!  

## Заключение: Коллайдеры — Ядро Твоего Боя! 💻
Ты освоил коллайдеры, сделав пули смертельными, а бонусы полезными. Твой шутер — настоящая арена! Следующий шаг — корутины для задержек перезарядки. Продолжай, воин кода!

**Что Далее?**  
- Перейди к [Корутины — Задержки для Перезарядки](../Intermediate/Lesson8_Coroutines.md) — добавь тайминг.  
- Вопросы: [Unity Learn: Коллайдеры](https://learn.unity.com/tutorial/colliders-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
