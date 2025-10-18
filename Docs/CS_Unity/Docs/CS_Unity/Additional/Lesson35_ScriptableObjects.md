
# 📜 Урок 35: Scriptable Objects — Гибкие Данные

Привет, юный хранитель данных арены! 💻 Добро пожаловать на пятый уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **Scriptable Objects** в Unity, чтобы сделать данные в твоём топ-даун шутере (*UnityTopDownShooterQuest*) гибкими и удобными. Ты создашь Scriptable Objects для конфигурации врагов и бонусов! Готов упростить настройки? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool`, `EnemyConfig` из [Урок 34: Частицы](../Additional/Lesson34_Particles.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `FastEnemy`, `HealthBonus`, `EnemyExplosion`, `BulletHitEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка ScriptableObjects в Assets — твоя база данных!  

**Предупреждение**: Scriptable Objects требуют правильной настройки и ссылок. Сохраняй код (**Ctrl+S**) и активы перед тестом, иначе данные не загрузятся! Если настройки не применяются, проверь ссылки в **Inspector**. Врубай Play Mode и управляй данными!

Готов создать гибкие данные? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Scriptable Objects?**
**Scriptable Objects** — это классы в Unity для хранения данных, независимых от объектов сцены.  
- **CreateAssetMenu**: Создаёт Scriptable Object в **Project**.  
- **SerializeField**: Делает поля редактируемыми в **Inspector**.  
- **Реюзабильность**: Один объект для множества настроек.  

В твоём шутере ты заменишь `EnemyConfig` на Scriptable Objects для врагов и бонусов.

*Ссылка на документацию*: [Scriptable Objects](https://docs.unity3d.com/Manual/class-ScriptableObject.html).

## 🔄 **Зачем это Нужно?**
Scriptable Objects упрощают управление настройками и повторное использование данных. В этом квесте ты:  
- Создашь Scriptable Objects для врагов и бонусов.  
- Настроишь спавн с их использованием.  
- Сделаешь игру гибкой и масштабируемой!  

**Почему это круто?**  
- **Гибкость**: Легко менять настройки без кода.  
- **Экономия**: Меньше дублирования данных.  
- **Для шутера**: Быстрая настройка врагов и бонусов!  

**Типичные ошибки новичков**:  
- Неправильные ссылки на Scriptable Objects в **Inspector**.  
- Забыл создать актив через **CreateAssetMenu**.  
- Изменение данных в рантайме вместо редактора.  

## ⚙️ **Квест: Создай гибкие данные**

### Уровень 1: Scriptable Object для врагов  
1. **Создай Scriptable Object:**  
   - В **Project** создай **C# Script**, назови `EnemyData`.  
   - Реализуй данные:  
     ```csharp
     using UnityEngine;

     [CreateAssetMenu(fileName = "NewEnemyData", menuName = "Enemy/Data")]
     public class EnemyData : ScriptableObject
     {
         public float speed = 2.0f;
         public int health = 50;
         public int damage = 10;
         public Material material;
     }
     ```

2. **Создай активы:**  
   - В **Project** → `Assets/ScriptableObjects`, создай два актива `EnemyData`: `NormalEnemyData` и `FastEnemyData`.  
   - Настрой:  
     - `NormalEnemyData`: Speed 2.0, Health 50, Damage 10, Material (стандартный).  
     - `FastEnemyData`: Speed 4.0, Health 30, Damage 15, Material (красный).  

3. **Обнови `NormalEnemy`:**  
   - Используй `EnemyData`:  
     ```csharp
     [SerializeField] private EnemyData enemyData;

     protected override void Start()
     {
         base.Start();
         player = GameObject.FindGameObjectWithTag("Player").transform;
         patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
         moveSpeed = enemyData.speed;
         currentHealth = enemyData.health;
         damage = enemyData.damage;
         GetComponent<Renderer>().material = enemyData.material;
     }
     ```

4. **Настрой `Enemy.prefab` и `FastEnemy.prefab`:**  
   - Перетащи `NormalEnemyData` в `Enemy.prefab`.  
   - Перетащи `FastEnemyData` в `FastEnemy.prefab`.  

5. **Настрой и протестируй:**  
   - Нажми **Play** — враги используют настройки из Scriptable Objects!  

### Уровень 2: Scriptable Object для бонусов  
1. **Создай Scriptable Object:**  
   - В **Project** создай **C# Script**, назови `BonusData`.  
   - Реализуй данные:  
     ```csharp
     using UnityEngine;

     [CreateAssetMenu(fileName = "NewBonusData", menuName = "Bonus/Data")]
     public class BonusData : ScriptableObject
     {
         public int minHealthBoost = 20;
         public int maxHealthBoost = 50;
         public float jumpForce = 3.0f;
         public Material glowMaterial;
     }
     ```

2. **Создай актив:**  
   - В **Project** → `Assets/ScriptableObjects`, создай актив `BonusData`, назови `HealthBonusData`.  
   - Настрой: Min Health Boost 20, Max Health Boost 50, Jump Force 3.0, Glow Material (BonusGlow).  

3. **Обнови `HealthBonus`:**  
   - Используй `BonusData`:  
     ```csharp
     [SerializeField] private BonusData bonusData;

     void Start()
     {
         GameManager.Instance.AddBonus(this);
         healthBoost = Random.Range(bonusData.minHealthBoost, bonusData.maxHealthBoost);
         Rigidbody rb = GetComponent<Rigidbody>();
         rb.AddForce(Vector3.up * bonusData.jumpForce, ForceMode.Impulse);
         GetComponent<Renderer>().material = bonusData.glowMaterial;
         Debug.Log($"Бонус с здоровьем: {healthBoost}, подпрыгнул!");
     }
     ```

4. **Настрой `HealthBonus.prefab`:**  
   - Перетащи `HealthBonusData` в поле `Bonus Data`.  

5. **Настрой и протестируй:**  
   - Нажми **Play** — бонусы используют настройки из Scriptable Objects!  

### Уровень 3: Масштабирование настроек  
1. **Обнови `EnemySpawner`:**  
   - Добавь массив данных:  
     ```csharp
     [SerializeField] private EnemyData[] enemyDataOptions;

     [Command]
     void CmdSpawnEnemy()
     {
         GameObject prefabToSpawn;
         float rand = Random.value;
         EnemyData selectedData = enemyDataOptions[Random.Range(0, enemyDataOptions.Length)];
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
         if (prefabToSpawn != bossEnemyPrefab)
         {
             enemy.GetComponent<NormalEnemy>().SetEnemyData(selectedData);
         }
         NetworkServer.Spawn(enemy);
         Debug.Log($"Создан враг: {prefabToSpawn.name} с данными {selectedData.name}");
     }
     ```

2. **Обнови `NormalEnemy`:**  
   - Добавь метод:  
     ```csharp
     public void SetEnemyData(EnemyData data)
     {
         enemyData = data;
         moveSpeed = data.speed;
         currentHealth = data.health;
         damage = data.damage;
         GetComponent<Renderer>().material = data.material;
     }
     ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `EnemySpawner`, добавь `NormalEnemyData` и `FastEnemyData` в массив `Enemy Data Options`.  
   - Нажми **Play** — враги спавнятся с разными настройками!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для Scriptable Objects в префабах и `EnemySpawner`.  
- В **Scene/Game**: Враги и бонусы с кастомными настройками.  
- В **Console**: Логи спавна и настроек.  

## 💡 **Квесты: Прокачай Scriptable Objects!**
1. **Базовый квест**:  
   - Создай новый `EnemyData` с `speed` 3.0 и `health` 70.  
   - Проверь: враги используют новые настройки!  

2. **Квест на бонусы**:  
   - В `BonusData` добавь поле `rotationSpeed`:  
     ```csharp
     public float rotationSpeed = 90.0f;
     ```  
   - В `HealthBonus`:  
     ```csharp
     void Update()
     {
         transform.Rotate(0, bonusData.rotationSpeed * Time.deltaTime, 0);
     }
     ```  
   - Проверь: бонусы вращаются с кастомной скоростью!  

3. **Квест на массив**:  
   - Добавь ещё один `EnemyData` в массив `EnemySpawner`.  
   - Проверь: больше разнообразия врагов!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Создай `BonusData` для бонуса урона:  
     - В `BonusData`:  
       ```csharp
       public bool isDamageBoost;
       public int damageBoost = 10;
       ```  
     - В `HealthBonus`:  
       ```csharp
       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Player"))
           {
               var player = other.GetComponent<PlayerController>();
               if (bonusData.isDamageBoost)
                   player.IncreaseDamage(bonusData.damageBoost);
               else
                   player.TakeDamage(-healthBoost);
               // ... остальной код ...
           }
       }
       ```  
     - В `PlayerController`:  
       ```csharp
       public void IncreaseDamage(int boost)
       {
           scorePerHit += boost;
           Debug.Log($"Урон увеличен на {boost}!");
       }
       ```  
   - Проверь: бонусы увеличивают урон игрока!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если данные не применяются:  
  - Сохранил ли код и активы (**Ctrl+S**)?  
  - Проверь ссылки на Scriptable Objects.  
  - Правильные ли данные в **Inspector**?  
- **Хочешь эпичности?** В `EnemyData` добавь:  
  ```csharp
  Debug.Log($"Применены данные: {name}");
  ```  
  - Увидишь сообщение в **Console**!  
- **Настройки не работают?** Проверь создание активов через **CreateAssetMenu**.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Scriptable Objects делают настройки гибкими и удобными, упрощая балансировку. Это ключ к масштабируемости!

## Заключение: Scriptable Objects — Твой Хранитель Данных! 💻
Ты освоил Scriptable Objects, упростив настройки. Твой шутер стал гибким! Следующий шаг — LINQ для умных запросов. Продолжай, хранитель данных!

**Что Далее?**  
- Перейди к [LINQ в Unity — Умные Запросы к Данным](../Additional/Lesson36_LINQ.md) — анализируй данные.  
- Вопросы: [Unity Learn: Scriptable Objects](https://learn.unity.com/tutorial/scriptable-objects).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
