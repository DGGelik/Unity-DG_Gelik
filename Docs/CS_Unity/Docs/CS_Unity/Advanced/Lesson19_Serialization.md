
# 🛠️ Урок 19: Сериализация — Настройки в Inspector

Привет, юный настройщик арены! 💻 Добро пожаловать на шестой уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **сериализацию** в C#, чтобы настраивать параметры твоего топ-даун шутера (*UnityTopDownShooterQuest*) прямо в **Inspector**. Сериализация позволяет задавать значения переменных в Unity без изменения кода. Ты настроишь параметры игрока, врагов и бонусов! Готов упростить настройку игры? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 18: Делегаты](../Advanced/Lesson18_Delegates.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя мастерская настроек!  

**Предупреждение**: Сериализация требует правильного использования атрибута `[SerializeField]`. Сохраняй код (**Ctrl+S**) перед тестом, иначе настройки не появятся в **Inspector**! Если поля не видны, проверь модификаторы доступа (`private`/`protected`). Врубай Play Mode и настрой игру!

Готов упростить настройку? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Сериализация?**
**Сериализация** в Unity позволяет отображать приватные поля в **Inspector** для настройки без изменения кода.  
- **Атрибут `[SerializeField]`**: Делает приватное поле видимым в **Inspector**.  
- **Публичные поля**: Автоматически отображаются, но лучше использовать `[SerializeField]` для контроля доступа.  

В твоём шутере ты добавишь сериализацию для параметров, чтобы легко настраивать игру.

*Ссылка на документацию*: [Serialization](https://docs.unity3d.com/Manual/script-Serialization.html).

## 🔄 **Зачем это Нужно?**
Сериализация упрощает настройку игры, позволяя менять значения прямо в Unity. В этом квесте ты:  
- Сделаешь параметры игрока настраиваемыми.  
- Настроишь врагов и бонусы в **Inspector**.  
- Упростишь тестирование и балансировку игры!  

**Почему это круто?**  
- **Удобство**: Настройка без правки кода.  
- **Гибкость**: Быстрая смена параметров для тестирования.  
- **Для шутера**: Лёгкий баланс здоровья, скорости и урона!  

**Типичные ошибки новичков**:  
- Забыл добавить `[SerializeField]` к приватным полям.  
- Использование неподдерживаемых типов (например, `List` без сериализации).  
- Неправильные значения в **Inspector**.  

## ⚙️ **Квест: Настрой игру**

### Уровень 1: Сериализация параметров игрока  
1. **Обнови `PlayerController`:**  
   - Убедись, что все поля используют `[SerializeField]` для настройки:  
     ```csharp
     [SerializeField] private float moveSpeed = 5.0f;
     [SerializeField] private string playerName = "Hero";
     [SerializeField] private int maxHealth = 100;
     [SerializeField] private int bulletsPerShot = 3;
     [SerializeField] private float bulletSpeed = 10.0f;
     [SerializeField] private int maxAmmo = 10;
     [SerializeField] private float reloadTime = 1.0f;
     [SerializeField] private LayerMask shootableLayer;
     [SerializeField] private UnityEngine.UI.Slider healthBar;
     [SerializeField] private int scorePerHit = 50;
     [SerializeField] private AudioClip shotSound;
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Player`, измени `Move Speed` на 7.0 в **Inspector**.  
   - Нажми **Play** — игрок двигается быстрее!  

### Уровень 2: Сериализация врагов  
1. **Обнови `Enemy`:**  
   - Добавь атрибуты для всех полей:  
     ```csharp
     [SerializeField] protected int maxHealth = 100;
     [SerializeField] protected float moveSpeed = 2.0f;
     [SerializeField] protected GameObject explosionPrefab;
     [SerializeField] protected AudioClip explosionSound;
     ```

2. **Обнови `BossEnemy`:**  
   - Добавь сериализацию для урона:  
     ```csharp
     [SerializeField] private int damage = 20;
     ```

3. **Полный код `BossEnemy`:**  
   ```csharp
   using UnityEngine;

   public class BossEnemy : Enemy
   {
       [SerializeField] private Transform player;
       [SerializeField] private int damage = 20;

       protected override void Start()
       {
           base.Start();
           maxHealth = 300;
           moveSpeed = 3.0f;
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

4. **Настрой и протестируй:**  
   - Открой `Boss.prefab`, измени `Damage` на 30 в **Inspector**.  
   - Нажми **Play** — боссы наносят больше урона при столкновении!  

### Уровень 3: Сериализация бонусов и спавнера  
1. **Обнови `HealthBonus`:**  
   - Добавь сериализацию:  
     ```csharp
     [SerializeField] private int healthBoost = 25;
     [SerializeField] private GameObject healEffectPrefab;
     [SerializeField] private AudioClip bonusSound;
     ```

2. **Обнови `EnemySpawner`:**  
   - Добавь сериализацию:  
     ```csharp
     [SerializeField] private GameObject normalEnemyPrefab;
     [SerializeField] private GameObject bossEnemyPrefab;
     [SerializeField] private float spawnRange = 10.0f;
     [SerializeField] private float spawnInterval = 2.0f;
     [SerializeField] private float bossSpawnChance = 0.1f;
     ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `EnemySpawner`, уменьши `Spawn Interval` до 1.0.  
   - Открой `HealthBonus.prefab`, увеличь `Health Boost` до 50.  
   - Нажми **Play** — враги появляются чаще, бонусы дают больше здоровья!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для настройки скорости, здоровья, урона, интервалов.  
- В **Scene/Game**: Изменённые параметры влияют на геймплей.  
- В **Console**: Сообщения о действиях с учётом новых настроек.  

## 💡 **Квесты: Прокачай настройки!**
1. **Базовый квест**:  
   - В `PlayerController` измени `Bullets Per Shot` на 5 в **Inspector**.  
   - Проверь: игрок стреляет больше пуль!  

2. **Квест на врагов**:  
   - В `Boss.prefab` увеличь `Max Health` до 500.  
   - Проверь: боссы стали прочнее!  

3. **Квест на спавнер**:  
   - В `EnemySpawner` увеличь `Boss Spawn Chance` до 0.2.  
   - Проверь: боссы появляются чаще!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь в `PlayerController` настройку цвета игрока:  
     ```csharp
     [SerializeField] private Color playerColor = Color.white;

     void Start()
     {
         // ... существующий код ...
         GetComponent<Renderer>().material.color = playerColor;
     }
     ```  
   - В **Inspector** измени `Player Color` на красный.  
   - Проверь: игрок стал красным!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если поля не видны:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь `[SerializeField]` и модификаторы доступа.  
  - Поддерживаемый ли тип данных?  
- **Хочешь эпичности?** В `EnemySpawner` добавь:  
  ```csharp
  Debug.Log($"Настройки спавнера: интервал {spawnInterval}, шанс босса {bossSpawnChance}");
  ```  
  - Увидишь настройки в **Console**!  
- **Поля не обновляются?** Проверь значения по умолчанию в коде.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Сериализация упрощает балансировку и тестирование, позволяя настраивать игру без правки кода. Это ключ к гибкости!

## Заключение: Сериализация — Твой Инструмент Настройки! 💻
Ты освоил сериализацию, упростив настройку игры. Твой шутер стал гибким! Следующий шаг — математика и случайность с Mathf и Random. Продолжай, настройщик кода!

**Что Далее?**  
- Перейди к [Mathf и Random — Математика и Случайность](../Advanced/Lesson20_MathRandom.md) — добавь случайность.  
- Вопросы: [Unity Learn: Serialization](https://learn.unity.com/tutorial/serialization).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
