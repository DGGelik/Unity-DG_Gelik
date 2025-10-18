
# 🧱 Урок 33: Работа с Префабами — Повторное Использование

Привет, юный архитектор арены! 💻 Добро пожаловать на третий уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты углубишься в работу с **префабами** в Unity, чтобы упростить создание и управление объектами в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты создашь префаб для врагов с вариантами и настроишь их через код! Готов строить арену эффективно? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool` из [Урок 32: New Input System](../Additional/Lesson32_NewInput.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Prefabs в Assets — твоя строительная база!  

**Предупреждение**: Префабы требуют правильной настройки и обновления. Сохраняй код (**Ctrl+S**) и префабы перед тестом, иначе изменения не применятся! Если объекты не появляются, проверь ссылки на префабы в **Inspector**. Врубай Play Mode и строй арену!

Готов создавать префабы? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Префабы?**
**Префабы** в Unity — это шаблоны объектов, которые можно многократно использовать.  
- **Prefab Variants**: Варианты префабов с изменёнными настройками.  
- **Instantiate**: Создание экземпляра префаба в сцене.  
- **Apply Changes**: Применение изменений к префабу.  

В твоём шутере ты создашь префабы для врагов с разными характеристиками.

*Ссылка на документацию*: [Prefabs](https://docs.unity3d.com/Manual/Prefabs.html).

## 🔄 **Зачем это Нужно?**
Префабы упрощают создание и управление объектами, экономя время. В этом квесте ты:  
- Создашь префаб-вариант для быстрого врага.  
- Настроишь спавн врагов через код.  
- Сделаешь игру модульной и удобной!  

**Почему это круто?**  
- **Эффективность**: Один префаб — много объектов.  
- **Гибкость**: Варианты для разных врагов.  
- **Для шутера**: Быстрое создание врагов!  

**Типичные ошибки новичков**:  
- Забыл применить изменения к префабу (**Apply**).  
- Неправильные ссылки на префабы в скриптах.  
- Изменение экземпляра вместо префаба.  

## ⚙️ **Квест: Создай и настрой префабы**

### Уровень 1: Вариант префаба для быстрого врага  
1. **Создай префаб-вариант:**  
   - В **Project** → `Assets/Prefabs`, выбери `Enemy.prefab`, создай **Create Variant**, назови `FastEnemy.prefab`.  
   - В `FastEnemy.prefab` измени `NormalEnemy`:  
     - Установи `moveSpeed` на 4.0f (вместо 2.0f).  
     - Измени материал на новый (например, красный).  

2. **Обнови `EnemySpawner`:**  
   - Добавь спавн быстрого врага:  
     ```csharp
     [SerializeField] private GameObject fastEnemyPrefab;
     [SerializeField] private float fastEnemySpawnChance = 0.2f;

     [Command]
     void CmdSpawnEnemy()
     {
         GameObject prefabToSpawn;
         if (Random.value < bossSpawnChance)
             prefabToSpawn = bossEnemyPrefab;
         else if (Random.value < fastEnemySpawnChance)
             prefabToSpawn = fastEnemyPrefab;
         else
             prefabToSpawn = normalEnemyPrefab;

         Vector3 spawnPosition = new Vector3(
             Random.Range(-spawnRange, spawnRange),
             0,
             Random.Range(-spawnRange, spawnRange)
         );
         GameObject enemy = Instantiate(prefabToSpawn, spawnPosition, Quaternion.identity);
         NetworkServer.Spawn(enemy);
         Debug.Log($"Создан враг: {prefabToSpawn.name} на позиции {spawnPosition}");
     }
     ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `EnemySpawner`, перетащи `FastEnemy.prefab` в поле `Fast Enemy Prefab`.  
   - Нажми **Play** — появляются быстрые враги!  

### Уровень 2: Динамическая настройка префабов  
1. **Создай скрипт `EnemyConfig`:**  
   - В **Project** создай **C# Script**, назови `EnemyConfig`.  
   - Реализуй конфигурацию:  
     ```csharp
     using UnityEngine;

     public class EnemyConfig : MonoBehaviour
     {
         [SerializeField] private float speed = 2.0f;
         [SerializeField] private int health = 50;
         [SerializeField] private Material material;

         public void ApplyConfig(NormalEnemy enemy)
         {
             enemy.SetSpeed(speed);
             enemy.SetHealth(health);
             GetComponent<Renderer>().material = material;
         }
     }
     ```

2. **Обнови `NormalEnemy`:**  
   - Добавь методы настройки:  
     ```csharp
     public void SetSpeed(float newSpeed) => moveSpeed = newSpeed;
     public void SetHealth(int newHealth) => currentHealth = newHealth;
     ```

3. **Обнови `EnemySpawner`:**  
   - Применяй конфигурацию:  
     ```csharp
     [Command]
     void CmdSpawnEnemy()
     {
         GameObject prefabToSpawn;
         float rand = Random.value;
         if (rand < bossSpawnChance)
             prefabToSpawn = bossEnemyPrefab;
         else if (rand < fastEnemySpawnChance)
             prefabToSpawn = fastEnemyPrefab;
         else
             prefabToSpawn = normalEnemyPrefab;

         Vector3 spawnPosition = new Vector3(
             Random.Range(-spawnRange, spawnRange),
             0,
             Random.Range(-spawnRange, spawnRange)
         );
         GameObject enemy = Instantiate(prefabToSpawn, spawnPosition, Quaternion.identity);
         if (prefabToSpawn == normalEnemyPrefab)
         {
             EnemyConfig config = enemy.GetComponent<EnemyConfig>();
             if (config != null)
                 config.ApplyConfig(enemy.GetComponent<NormalEnemy>());
         }
         NetworkServer.Spawn(enemy);
         Debug.Log($"Создан враг: {prefabToSpawn.name} на позиции {spawnPosition}");
     }
     ```

4. **Настрой `NormalEnemy.prefab`:**  
   - Добавь компонент `EnemyConfig`, установи `Speed` на 2.5f, `Health` на 60, выбери новый материал.  

5. **Настрой и протестируй:**  
   - Нажми **Play** — обычные враги спавнятся с кастомными настройками!  

### Уровень 3: Обновление префабов  
1. **Обнови `Enemy.prefab`:**  
   - В **Hierarchy** измени `Enemy.prefab` (например, добавь новый эффект частиц).  
   - Нажми **Apply** в **Inspector** для сохранения изменений.  

2. **Настрой и протестируй:**  
   - Нажми **Play** — все враги используют обновлённый префаб!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для префабов и конфигураций в `EnemySpawner`.  
- В **Scene/Game**: Быстрые враги и кастомные обычные враги.  
- В **Console**: Логи спавна с названиями префабов.  

## 💡 **Квесты: Прокачай префабы!**
1. **Базовый квест**:  
   - Создай префаб-вариант `TankEnemy` с `moveSpeed` 1.0f и `currentHealth` 100.  
   - Проверь: танк-вражки появляются в игре!  

2. **Квест на конфигурацию**:  
   - В `EnemyConfig` добавь настройку урона:  
     ```csharp
     [SerializeField] private int damage = 10;
     public void ApplyConfig(NormalEnemy enemy)
     {
         enemy.SetSpeed(speed);
         enemy.SetHealth(health);
         enemy.SetDamage(damage);
         GetComponent<Renderer>().material = material;
     }
     ```  
   - Проверь: враги наносят кастомный урон!  

3. **Квест на обновление**:  
   - Измени материал `FastEnemy.prefab` и примени (**Apply**).  
   - Проверь: все быстрые враги используют новый материал!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Создай префаб-вариант `ExplosiveEnemy`, который взрывается при смерти:  
     - В `NormalEnemy`:  
       ```csharp
       protected override void Die()
       {
           if (gameObject.CompareTag("ExplosiveEnemy"))
           {
               Collider[] colliders = Physics.OverlapSphere(transform.position, 3.0f);
               foreach (var collider in colliders)
               {
                   if (collider.CompareTag("Player"))
                       collider.GetComponent<PlayerController>().TakeDamage(20);
               }
           }
           base.Die();
       }
       ```  
   - Проверь: взрыв наносит урон игроку!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если префабы не работают:  
  - Сохранил ли код и префабы (**Ctrl+S**)?  
  - Проверь ссылки на префабы в **Inspector**.  
  - Применил ли изменения (**Apply**)?  
- **Хочешь эпичности?** В `EnemySpawner` добавь:  
  ```csharp
  Debug.Log($"Спавн префаба: {prefabToSpawn.name}!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Объекты не появляются?** Проверь `Instantiate` и `NetworkServer.Spawn`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Префабы упрощают создание и управление объектами, делая игру масштабируемой. Это ключ к эффективности!

## Заключение: Префабы — Твой Строитель Арены! 💻
Ты освоил префабы, добавив гибкость врагам. Твой шутер стал модульным! Следующий шаг — частицы через код. Продолжай, архитектор кода!

**Что Далее?**  
- Перейди к [Частицы через Код — Взрывы и Эффекты](../Additional/Lesson34_Particles.md) — добавь эффекты.  
- Вопросы: [Unity Learn: Prefabs](https://learn.unity.com/tutorial/prefabs).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
