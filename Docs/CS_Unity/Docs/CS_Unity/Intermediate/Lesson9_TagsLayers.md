
# 🏷️ Урок 9: Теги и Слои — Отдели Героя от Врагов!

Привет, юный организатор арены! 💻 Добро пожаловать на третий уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером порядка**, освоив **теги** и **слои** в Unity, чтобы разделить героя, врагов и бонусы в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Теги и слои — это как ярлыки и папки, которые помогают управлять объектами и столкновениями. Ты сделаешь так, чтобы пули не попадали по герою, а бонусы — только для него! Готов навести порядок? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus` из [Урок 8: Корутины](../Intermediate/Lesson8_Coroutines.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твой центр организации!  

**Предупреждение**: Теги и слои должны точно совпадать в коде и **Inspector**. Сохраняй код (**Ctrl+S**) перед тестом, иначе столкновения могут не работать! Если пули бьют героя, проверь теги и настройки слоёв. Врубай Play Mode и организуй поле битвы!

Готов упорядочить арену? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Теги и Слои?**
- **Теги**: Метки (например, "Player", "Enemy") для идентификации объектов в коде.  
- **Слои**: Группы (например, "PlayerLayer", "EnemyLayer") для управления столкновениями и рендерингом.  

В твоём шутере теги обеспечат попадания пуль только по врагам, а бонусы — только для героя, а слои предотвратят ненужные столкновения.

*Ссылка на документацию*: [Теги и Слои](https://docs.unity3d.com/Manual/TagsAndLayers.html).

## 🔄 **Зачем это Нужно?**
Теги и слои упрощают игру, предотвращая хаос, например, попадания пуль по герою. В этом квесте ты:  
- Уточнишь логику столкновений с помощью тегов.  
- Настроишь слои, чтобы пули не били героя.  
- Сделаешь арену чистой и эффективной!

**Почему это круто?**  
- **Точность**: Пули бьют только врагов, бонусы помогают только герою.  
- **Порядок**: Слои контролируют столкновения, избегая багов.  
- **Для шутера**: Это обеспечивает плавную и профессиональную боевую механику!

**Типичные ошибки новичков**:  
- Ошибка в написании тега в коде (например, "enemy" вместо "Enemy").  
- Забыл назначить теги или слои в **Inspector**.  
- Неправильные настройки матрицы столкновений в Physics.

## ⚙️ **Квест: Организуй арену с тегами и слоями**

### Уровень 1: Уточни теги для столкновений  
1. **Проверь теги:**  
   - В **Hierarchy** убедись, что `Player` имеет тег `Player`, а `Enemy.prefab` — тег `Enemy` (**Inspector > Tag**).  
   - Для `HealthBonus.prefab` убедись, что скрипт проверяет тег `Player`.  

2. **Обнови `BulletController` для точности тегов:**  
   - Убедись, что код наносит урон только врагам:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class BulletController : MonoBehaviour
     {
         [SerializeField] private int damage = 20;

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

3. **Обнови `HealthBonus` для взаимодействия только с игроком:**  
   - Убедись, что код лечит только игрока:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class HealthBonus : MonoBehaviour
     {
         [SerializeField] private int healthBoost = 25;

         void OnTriggerEnter(Collider other)
         {
             if (other.CompareTag("Player"))
             {
                 other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
                 Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
                 Destroy(gameObject);
             }
         }
     }
     ```

4. **Протестируй теги:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что теги `Player` и `Enemy` установлены правильно.  
   - Нажми **Play**, стреляй (**Space**) по врагам, собирай бонусы героем.  
   - В **Console**: пули бьют только врагов, бонусы лечат только героя.  

### Уровень 2: Настрой слои для управления столкновениями  
1. **Создай слои:**  
   - В **Inspector** щёлкни на **Layer** у любого объекта, выбери **Add Layer**.  
   - Добавь:  
     - User Layer 8: `PlayerLayer`  
     - User Layer 9: `EnemyLayer`  
     - User Layer 10: `BulletLayer`  
     - User Layer 11: `BonusLayer`  

2. **Назначь слои:**  
   - Установи `Player` на `PlayerLayer`.  
   - Установи `Enemy.prefab` на `EnemyLayer`.  
   - Установи `Bullet.prefab` на `BulletLayer`.  
   - Установи `HealthBonus.prefab` на `BonusLayer`.  

3. **Настрой матрицу столкновений:**  
   - Перейди в **Edit > Project Settings > Physics**.  
   - В **Layer Collision Matrix** сними галочки:  
     - `PlayerLayer` vs. `BulletLayer` (чтобы пули не били героя).  
     - `EnemyLayer` vs. `EnemyLayer` (чтобы враги не сталкивались друг с другом).  
     - `BulletLayer` vs. `BonusLayer` (пули не бьют бонусы).  
   - Убедись, что включены: `BulletLayer` vs. `EnemyLayer` и `PlayerLayer` vs. `BonusLayer`.  

4. **Протестируй слои:**  
   - Нажми **Play**, стреляй по врагам, двигай героя к бонусам.  
   - Пули бьют врагов, но не героя, бонусы работают только для героя!  

### Уровень 3: Улучши спавнер с тегами и слоями  
1. **Обнови `EnemySpawner`:**  
   - Убедись, что спавненные враги и бонусы получают правильные теги и слои:  
     ```csharp
     private IEnumerator SpawnWave()
     {
         int spawnedEnemies = 0;
         while (spawnedEnemies < enemiesToSpawn)
         {
             GameObject enemy = Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
             enemy.tag = "Enemy";
             enemy.layer = LayerMask.NameToLayer("EnemyLayer");
             Debug.Log("Спавн врага " + (spawnedEnemies + 1));
             spawnedEnemies++;
             yield return new WaitForSeconds(spawnDelay);
         }
         GameObject bonus = Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
         bonus.layer = LayerMask.NameToLayer("BonusLayer");
         Debug.Log("Спавн бонуса здоровья!");
     }
     ```

2. **Полный код `EnemySpawner`:**  
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
           StartCoroutine(SpawnWave());
       }

       private IEnumerator SpawnWave()
       {
           int spawnedEnemies = 0;
           while (spawnedEnemies < enemiesToSpawn)
           {
               GameObject enemy = Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
               enemy.tag = "Enemy";
               enemy.layer = LayerMask.NameToLayer("EnemyLayer");
               Debug.Log("Спавн врага " + (spawnedEnemies + 1));
               spawnedEnemies++;
               yield return new WaitForSeconds(spawnDelay);
           }
           GameObject bonus = Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
           bonus.layer = LayerMask.NameToLayer("BonusLayer");
           Debug.Log("Спавн бонуса здоровья!");
       }
   }
   ```

3. **Протестируй спавнер:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `Enemy.prefab` и `HealthBonus.prefab` привязаны в `Spawner`.  
   - Нажми **Play**: враги спавнятся на `EnemyLayer`, бонус — на `BonusLayer`, столкновения работают корректно!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Теги (`Player`, `Enemy`) и слои (`PlayerLayer`, `EnemyLayer`, и т.д.).  
- В **Scene/Game**: Пули бьют только врагов, бонусы лечат только героя.  
- В **Console**: Сообщения о попаданиях, лечении и спавне.  

## 💡 **Квесты: Организуй арену!**
1. **Базовый квест**:  
   - В **Inspector** установи тег `Player` и слой `PlayerLayer` для `Player`.  
   - Для `Enemy.prefab` установи тег `Enemy` и слой `EnemyLayer`.  
   - Проверь: пули не бьют героя, только врагов.  

2. **Квест на слои**:  
   - В **Physics Settings** сними галочку `BonusLayer` vs. `EnemyLayer`.  
   - Проверь: враги не могут собирать бонусы.  

3. **Квест с двумя спавнерами**:  
   - Создай второй `Spawner2`, установи `Enemies To Spawn` = 3, позиция (x=0, y=0, z=-5).  
   - Проверь: два спавнера создают врагов на `EnemyLayer` и бонусы на `BonusLayer`.  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь тег `Boss` и слой `BossLayer`.  
   - Создай `Boss.prefab` (скопируй `Enemy.prefab`, переименуй, установи тег `Boss`, слой `BossLayer`).  
   - В `BulletController` добавь урон для босса:  
     ```csharp
     void OnTriggerEnter(Collider other)
     {
         if (other.CompareTag("Enemy"))
         {
             other.GetComponent<EnemyStats>().TakeDamage(damage);
             Debug.Log("Пуля попала в " + other.name + "!");
             Destroy(gameObject);
         }
         else if (other.CompareTag("Boss"))
         {
             other.GetComponent<EnemyStats>().TakeDamage(damage / 2);
             Debug.Log("Пуля попала в БОССА " + other.name + " на половинный урон!");
             Destroy(gameObject);
         }
     }
     ```  
   - В `EnemySpawner` добавь спавн босса:  
     ```csharp
     GameObject boss = Instantiate(bossPrefab, new Vector3(0, 0, 10), Quaternion.identity);
     boss.tag = "Boss";
     boss.layer = LayerMask.NameToLayer("BossLayer");
     ```  
   - Проверь: пули наносят боссу половинный урон!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если столкновения сбоят:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Правильно ли назначены теги и слои?  
  - Проверь **Physics Collision Matrix**.  
- **Хочешь эпичности?** В `EnemySpawner` добавь:  
  ```csharp
  Debug.Log("Враг " + (spawnedEnemies + 1) + " на слое: " + LayerMask.LayerToName(enemy.layer));
  ```  
  - Увидишь назначение слоёв в **Console**!  
- **Неправильные столкновения?** Проверь настройки матрицы слоёв.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Теги и слои делают игру чистой и без багов, обеспечивая правильную работу пуль и бонусов. Это ключ к профессиональной боевой системе!

## Заключение: Теги и Слои — Твои Органайзеры Арены! 💻
Ты освоил теги и слои, сделав столкновения точными. Твоя арена организована! Следующий шаг — Raycast для прицельной стрельбы. Продолжай, организатор кода!

**Что Далее?**  
- Перейди к [Raycast — Стрельба по Прицелу](../Intermediate/Lesson10_Raycast.md) — целься как профи.  
- Вопросы: [Unity Learn: Теги и Слои](https://learn.unity.com/tutorial/tags-and-layers).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
