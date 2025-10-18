
# 🔢 Урок 20: Mathf и Random — Математика и Случайность

Привет, юный математик арены! 💻 Добро пожаловать на седьмой уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь классы **Mathf** и **Random** в Unity, чтобы добавить математику и случайность в твой топ-даун шутер (*UnityTopDownShooterQuest*). Ты создашь случайные позиции спавна, волнообразное движение врагов и случайные бонусы! Готов добавить хаос и расчёты? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 19: Сериализация](../Advanced/Lesson19_Serialization.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя лаборатория математики!  

**Предупреждение**: Random требует правильной настройки диапазонов, а Mathf — точных расчётов. Сохраняй код (**Ctrl+S**) перед тестом, иначе движения или спавн не сработают! Если враги или бонусы появляются не там, проверь диапазоны в `Random.Range`. Врубай Play Mode и добавь хаос!

Готов рассчитать арену? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Mathf и Random?**
**Mathf** — это класс Unity для математических операций, таких как синус, косинус, округление. **Random** отвечает за генерацию случайных чисел.  
- **Mathf**: Используется для плавных движений, интерполяции, ограничений (например, `Mathf.Sin`, `Mathf.Clamp`).  
- **Random**: Генерирует случайные значения (например, `Random.Range`).  

В твоём шутере ты добавишь волнообразное движение врагов и случайные позиции спавна.

*Ссылка на документацию*: [Mathf](https://docs.unity3d.com/ScriptReference/Mathf.html), [Random](https://docs.unity3d.com/ScriptReference/Random.html).

## 🔄 **Зачем это Нужно?**
Математика и случайность делают игру динамичной и непредсказуемой. В этом квесте ты:  
- Создашь случайные позиции спавна бонусов.  
- Добавишь волнообразное движение врагам.  
- Сделаешь игру разнообразной и живой!  

**Почему это круто?**  
- **Динамика**: Случайность делает каждый бой уникальным.  
- **Плавность**: Mathf создаёт естественные движения.  
- **Для шутера**: Хаос и движение усиливают геймплей!  

**Типичные ошибки новичков**:  
- Неправильные диапазоны в `Random.Range`.  
- Забыл умножить `Time.time` в `Mathf.Sin` для анимации.  
- Неправильное использование `Mathf.Clamp` для ограничений.  

## ⚙️ **Квест: Добавь хаос и движение**

### Уровень 1: Случайный спавн бонусов  
1. **Создай скрипт `BonusSpawner`:**  
   - В **Project** создай **C# Script**, назови `BonusSpawner`.  
   - Реализуй случайный спавн:  
     ```csharp
     using UnityEngine;
     using System.Collections;

     public class BonusSpawner : MonoBehaviour
     {
         [SerializeField] private GameObject bonusPrefab;
         [SerializeField] private float spawnRange = 10.0f;
         [SerializeField] private float spawnInterval = 5.0f;

         void Start()
         {
             StartCoroutine(SpawnRoutine());
         }

         private IEnumerator SpawnRoutine()
         {
             while (true)
             {
                 Vector3 spawnPosition = new Vector3(
                     Random.Range(-spawnRange, spawnRange),
                     0,
                     Random.Range(-spawnRange, spawnRange)
                 );
                 Instantiate(bonusPrefab, spawnPosition, Quaternion.identity);
                 Debug.Log($"Создан бонус на позиции {spawnPosition}");
                 yield return new WaitForSeconds(spawnInterval);
             }
         }
     }
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** создай пустой объект `BonusSpawner`, добавь компонент `BonusSpawner`.  
   - Перетащи `HealthBonus.prefab` в поле `Bonus Prefab`.  
   - Нажми **Play** — бонусы появляются в случайных местах!  

### Уровень 2: Волнообразное движение врагов  
1. **Обнови `NormalEnemy`:**  
   - Добавь волнообразное движение с `Mathf.Sin`:  
     ```csharp
     void Update()
     {
         if (player != null)
         {
             Vector3 direction = (player.position - transform.position).normalized;
             float wave = Mathf.Sin(Time.time * 2.0f) * 0.5f;
             Vector3 waveOffset = new Vector3(wave, 0, 0);
             transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
         }
     }
     ```

2. **Полный код `NormalEnemy`:**  
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
               float wave = Mathf.Sin(Time.time * 2.0f) * 0.5f;
               Vector3 waveOffset = new Vector3(wave, 0, 0);
               transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
           }
       }
   }
   ```

3. **Настрой и протестируй:**  
   - Убедись, что `Enemy.prefab` имеет компонент `NormalEnemy`.  
   - Нажми **Play** — враги движутся к игроку с волнообразным смещением!  

### Уровень 3: Случайные параметры бонусов  
1. **Обнови `HealthBonus`:**  
   - Добавь случайное здоровье:  
     ```csharp
     void Start()
     {
         GameManager.Instance.AddBonus(this);
         healthBoost = Random.Range(20, 50);
         Debug.Log($"Бонус с здоровьем: {healthBoost}");
     }
     ```

2. **Полный код `HealthBonus`:**  
   ```csharp
   using UnityEngine;

   public class HealthBonus : MonoBehaviour
   {
       [SerializeField] private int healthBoost = 25;
       [SerializeField] private GameObject healEffectPrefab;
       [SerializeField] private AudioClip bonusSound;

       public delegate void OnBonusCollected(HealthBonus bonus);
       public static event OnBonusCollected BonusCollected;

       void Start()
       {
           GameManager.Instance.AddBonus(this);
           healthBoost = Random.Range(20, 50);
           Debug.Log($"Бонус с здоровьем: {healthBoost}");
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
               BonusCollected?.Invoke(this);
               Destroy(gameObject);
           }
       }
   }
   ```

3. **Настрой и протестируй:**  
   - Убедись, что `HealthBonus.prefab` настроен.  
   - Нажми **Play**, собери бонусы — они дают случайное здоровье!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для настройки спавна и параметров.  
- В **Scene/Game**: Бонусы появляются случайно, враги движутся волнообразно.  
- В **Console**: Сообщения о спавне и здоровье бонусов.  

## 💡 **Квесты: Прокачай хаос!**
1. **Базовый квест**:  
   - В `BonusSpawner` уменьши `Spawn Interval` до 3.0.  
   - Проверь: бонусы появляются чаще!  

2. **Квест на движение**:  
   - В `NormalEnemy` увеличь частоту волны до 4.0f:  
     ```csharp
     float wave = Mathf.Sin(Time.time * 4.0f) * 0.5f;
     ```  
   - Проверь: враги движутся быстрее!  

3. **Квест на бонусы**:  
   - В `HealthBonus` измени диапазон здоровья:  
     ```csharp
     healthBoost = Random.Range(10, 100);
     ```  
   - Проверь: бонусы дают больше здоровья!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь случайное вращение врагов:  
     - В `NormalEnemy`:  
       ```csharp
       transform.Rotate(0, Random.Range(-30f, 30f) * Time.deltaTime, 0);
       ```  
   - Проверь: враги вращаются случайно!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если спавн или движение не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь диапазоны в `Random.Range`.  
  - Правильный ли множитель в `Mathf.Sin`?  
- **Хочешь эпичности?** В `BonusSpawner` добавь:  
  ```csharp
  Debug.Log($"Спавн бонуса в радиусе {spawnRange}");
  ```  
  - Увидишь радиус в **Console**!  
- **Бонусы вне арены?** Проверь `spawnRange`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Mathf и Random делают игру непредсказуемой и динамичной, добавляя разнообразие в движение и спавн. Это ключ к увлекательному геймплею!

## Заключение: Random — Твой Генератор Хаоса! 💻
Ты освоил Mathf и Random, добавив случайность и движение. Твой шутер стал живым! Следующий шаг — плавные движения с Time и DeltaTime. Продолжай, математик кода!

**Что Далее?**  
- Перейди к [Time и DeltaTime — Плавные Движения](../Advanced/Lesson21_Time.md) — сделай игру плавной.  
- Вопросы: [Unity Learn: Mathf](https://learn.unity.com/tutorial/mathf).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
