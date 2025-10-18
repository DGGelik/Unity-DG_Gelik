
# ⚡ Урок 18: Делегаты и Events — Кастомные Сигналы

Привет, юный связист арены! 💻 Добро пожаловать на пятый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **делегаты** и **события** в C#, чтобы создать кастомные сигналы в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Делегаты позволяют скриптам "разговаривать" друг с другом через события, такие как смерть игрока или уничтожение врага. Ты добавишь сигналы для этих событий! Готов наладить связь? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 17: Singleton](../Advanced/Lesson17_Singleton.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя станция связи!  

**Предупреждение**: Делегаты требуют правильной подписки и отписки от событий. Сохраняй код (**Ctrl+S**) перед тестом, иначе события не сработают! Если сигналы не доходят, проверь подписку в `OnEnable`/`OnDisable`. Врубай Play Mode и налаживай связь!

Готов отправить сигналы? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Делегаты и События?**
**Делегаты** в C# — это типы, определяющие сигнатуру методов. **События** позволяют вызывать методы в ответ на действия.  
- **Делегат**: Шаблон метода (например, `public delegate void OnPlayerDeath()`).  
- **Событие**: Сигнал, вызывающий методы подписчиков (например, `event OnPlayerDeath PlayerDied`).  

В твоём шутере ты создашь события для смерти игрока и уничтожения врагов, чтобы `GameManager` реагировал на них.

*Ссылка на документацию*: [C# Delegates](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/).

## 🔄 **Зачем это Нужно?**
События позволяют скриптам взаимодействовать без жёсткой привязки. В этом квесте ты:  
- Добавишь событие смерти игрока для перезапуска игры.  
- Реализуешь событие уничтожения врага для начисления очков.  
- Сделаешь игру гибкой и модульной!  

**Почему это круто?**  
- **Модульность**: Скрипты независимы, но связаны событиями.  
- **Гибкость**: Легко добавлять новые реакции на события.  
- **Для шутера**: Упрощает управление ключевыми моментами игры!  

**Типичные ошибки новичков**:  
- Забыл подписаться/отписаться от события (`+=`/`-=`).  
- Неправильная сигнатура делегата.  
- Вызов события без проверки подписчиков.  

## ⚙️ **Квест: Наладь сигналы**

### Уровень 1: Событие смерти игрока  
1. **Обнови `PlayerController`:**  
   - Добавь делегат и событие:  
     ```csharp
     public delegate void OnPlayerDeath();
     public static event OnPlayerDeath PlayerDied;
     ```

   - В `TakeDamage` вызови событие:  
     ```csharp
     if (currentHealth <= 0)
     {
         Debug.Log(playerName + " повержен!");
         PlayerDied?.Invoke();
         Destroy(gameObject);
     }
     ```

2. **Обнови `GameManager`:**  
   - Подпишись на событие:  
     ```csharp
     void OnEnable()
     {
         PlayerController.PlayerDied += OnPlayerDeath;
     }

     void OnDisable()
     {
         PlayerController.PlayerDied -= OnPlayerDeath;
     }

     private void OnPlayerDeath()
     {
         RestartGame();
         Debug.Log("Событие: Игрок умер, игра перезапущена!");
     }
     ```

3. **Полный код `PlayerController`:**  
   ```csharp
   using System.Collections;
   using UnityEngine;
   using TMPro;

   public class PlayerController : MonoBehaviour
   {
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
       private int currentHealth;
       private int currentAmmo;
       private bool canShoot = true;
       private Camera mainCamera;
       private Animator animator;
       private AudioSource audioSource;

       public delegate void OnPlayerDeath();
       public static event OnPlayerDeath PlayerDied;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
           animator = GetComponent<Animator>();
           audioSource = GetComponent<AudioSource>();
           healthBar.maxValue = maxHealth;
           healthBar.value = currentHealth;
           Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60);
       }

       void Update()
       {
           float moveX = Input.GetAxis("Horizontal");
           float moveY = Input.GetAxis("Vertical");
           Vector3 movement = new Vector3(moveX, 0, moveY);
           transform.Translate(movement * moveSpeed * Time.deltaTime);
           float speed = movement.magnitude;
           animator.SetFloat("Speed", speed);

           if (currentHealth < 50)
           {
               moveSpeed = 2.0f;
               Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
           }
           else
           {
               moveSpeed = 5.0f;
           }

           if (Input.GetKeyDown(KeyCode.Space) && canShoot && currentAmmo >= bulletsPerShot)
           {
               StartCoroutine(ShootWithReload());
           }
           else if (Input.GetKeyDown(KeyCode.Space))
           {
               Debug.Log("Нельзя стрелять! Перезарядка или нет патронов!");
           }

           if (Input.GetKeyDown(KeyCode.R))
           {
               GameManager.Instance.RestartGame();
           }

           if (Input.GetKeyDown(KeyCode.P))
           {
               GameManager.Instance.TogglePause();
           }
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           healthBar.value = currentHealth;
           Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
           if (damage < 0)
           {
               GameManager.Instance.AddScore(10);
           }
           if (currentHealth <= 0)
           {
               Debug.Log(playerName + " повержен!");
               PlayerDied?.Invoke();
               Destroy(gameObject);
           }
       }

       private IEnumerator ShootWithReload()
       {
           canShoot = false;
           audioSource.PlayOneShot(shotSound);
           Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
           RaycastHit hit;
           if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
           {
               Vector3 direction = (hit.point - transform.position).normalized;
               for (int i = 0; i < bulletsPerShot; i++)
               {
                   GameObject bullet = BulletPool.Instance.GetBullet();
                   bullet.transform.position = transform.position + direction * 0.5f;
                   bullet.transform.rotation = Quaternion.identity;
                   bullet.SetActive(true);
                   bullet.GetComponent<Rigidbody>().velocity = direction * bulletSpeed;
                   Debug.Log(playerName + " выстрелил пулей " + (i + 1) + " в сторону " + hit.point + "!");
               }
               if (hit.collider != null && (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss")))
               {
                   GameManager.Instance.AddScore(scorePerHit);
                   Debug.Log("Прицел нацелен на " + hit.collider.name + "!");
               }
           }
           currentAmmo -= bulletsPerShot;
           Debug.Log("Осталось патронов: " + currentAmmo);
           yield return new WaitForSeconds(reloadTime);
           canShoot = true;
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Убедись, что `Player` и `GameManager` настроены правильно.  
   - Нажми **Play**, доведи здоровье игрока до 0 — игра перезапускается через событие!  

### Уровень 2: Событие уничтожения врага  
1. **Обнови `Enemy`:**  
   - Добавь делегат и событие:  
     ```csharp
     public delegate void OnEnemyDeath(Enemy enemy);
     public static event OnEnemyDeath EnemyDied;
     ```

   - В `Die` вызови событие:  
     ```csharp
     EnemyDied?.Invoke(this);
     ```

2. **Полный код `Enemy`:**  
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

       public delegate void OnEnemyDeath(Enemy enemy);
       public static event OnEnemyDeath EnemyDied;

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
           EnemyDied?.Invoke(this);
           animator.SetTrigger("Die");
           Instantiate(explosionPrefab, transform.position, Quaternion.identity);
           AudioSource.PlayClipAtPoint(explosionSound, transform.position);
           Debug.Log($"{gameObject.name} уничтожен с взрывом!");
           Destroy(gameObject, 0.5f);
       }
   }
   ```

3. **Обнови `GameManager`:**  
   - Подпишись на событие:  
     ```csharp
     void OnEnable()
     {
         PlayerController.PlayerDied += OnPlayerDeath;
         Enemy.EnemyDied += OnEnemyDeath;
     }

     void OnDisable()
     {
         PlayerController.PlayerDied -= OnPlayerDeath;
         Enemy.EnemyDied -= OnEnemyDeath;
     }

     private void OnEnemyDeath(Enemy enemy)
     {
         AddScore(enemy is BossEnemy ? 100 : 50);
         Debug.Log($"Событие: Враг {enemy.name} уничтожен, добавлено очков!");
     }
     ```

4. **Полный код `GameManager`:**  
   ```csharp
   using System.Collections.Generic;
   using UnityEngine;
   using TMPro;

   public class GameManager : MonoBehaviour
   {
       public static GameManager Instance { get; private set; }
       private List<Enemy> enemies = new List<Enemy>();
       private List<HealthBonus> bonuses = new List<HealthBonus>();
       [SerializeField] private TextMeshProUGUI scoreText;
       [SerializeField] private TextMeshProUGUI statsText;
       private int totalScore = 0;

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

       void OnEnable()
       {
           PlayerController.PlayerDied += OnPlayerDeath;
           Enemy.EnemyDied += OnEnemyDeath;
       }

       void OnDisable()
       {
           PlayerController.PlayerDied -= OnPlayerDeath;
           Enemy.EnemyDied -= OnEnemyDeath;
       }

       void Update()
       {
           statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}, Очки: {totalScore}";
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

       public void AddScore(int score)
       {
           totalScore += score;
           scoreText.text = "Очки: " + totalScore;
           Debug.Log($"Очки добавлены: {score}, всего: {totalScore}");
       }

       public void RestartGame()
       {
           totalScore = 0;
           scoreText.text = "Очки: " + totalScore;
           foreach (var enemy in enemies.ToArray())
           {
               enemy.TakeDamage(1000);
           }
           foreach (var bonus in bonuses.ToArray())
           {
               Destroy(bonus.gameObject);
           }
           Debug.Log("Игра перезапущена!");
       }

       public void TogglePause()
       {
           Time.timeScale = Time.timeScale == 0 ? 1 : 0;
           Debug.Log($"Игра {(Time.timeScale == 0 ? "на паузе" : "возобновлена")}");
       }

       private void OnPlayerDeath()
       {
           RestartGame();
           Debug.Log("Событие: Игрок умер, игра перезапущена!");
       }

       private void OnEnemyDeath(Enemy enemy)
       {
           AddScore(enemy is BossEnemy ? 100 : 50);
           Debug.Log($"Событие: Враг {enemy.name} уничтожен, добавлено очков!");
       }
   }
   ```

5. **Настрой и протестируй:**  
   - Убедись, что `Enemy.prefab` и `Boss.prefab` имеют правильные компоненты.  
   - Нажми **Play**, уничтожай врагов — очки начисляются через событие, боссы дают больше очков!  

### Уровень 3: Событие сбора бонуса  
1. **Обнови `HealthBonus`:**  
   - Добавь делегат и событие:  
     ```csharp
     public delegate void OnBonusCollected(HealthBonus bonus);
     public static event OnBonusCollected BonusCollected;
     ```

   - В `OnTriggerEnter` вызови событие:  
     ```csharp
     BonusCollected?.Invoke(this);
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

3. **Обнови `GameManager`:**  
   - Подпишись на событие:  
     ```csharp
     void OnEnable()
     {
         PlayerController.PlayerDied += OnPlayerDeath;
         Enemy.EnemyDied += OnEnemyDeath;
         HealthBonus.BonusCollected += OnBonusCollected;
     }

     void OnDisable()
     {
         PlayerController.PlayerDied -= OnPlayerDeath;
         Enemy.EnemyDied -= OnEnemyDeath;
         HealthBonus.BonusCollected -= OnBonusCollected;
     }

     private void OnBonusCollected(HealthBonus bonus)
     {
         AddScore(20);
         Debug.Log("Событие: Бонус собран, добавлено 20 очков!");
     }
     ```

4. **Настрой и протестируй:**  
   - Убедись, что `HealthBonus.prefab` настроен правильно.  
   - Нажми **Play**, собери бонус — очки начисляются через событие!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Score Text` и `Stats Text` в `GameManager`.  
- В **Scene/Game**: Очки начисляются за врагов и бонусы, игра перезапускается при смерти игрока.  
- В **Console**: Сообщения о событиях (смерть, уничтожение, сбор).  

## 💡 **Квесты: Прокачай сигналы!**
1. **Базовый квест**:  
   - В `OnEnemyDeath` увеличь очки для боссов до 150.  
   - Проверь: боссы дают больше очков!  

2. **Квест на бонусы**:  
   - В `OnBonusCollected` добавь сообщение:  
     ```csharp
     Debug.Log($"Бонус {bonus.name} собран!");
     ```  
   - Проверь: сообщение в **Console**!  

3. **Квест на UI**:  
   - В `StatsText` добавь отображение событий:  
     ```csharp
     statsText.text += $"\nСобытий: {totalScore / 10}";
     ```  
   - Проверь: UI показывает статистику!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь событие для низкого здоровья игрока:  
     - В `PlayerController`:  
       ```csharp
       public delegate void OnLowHealth();
       public static event OnLowHealth LowHealth;

       if (currentHealth <= 30 && currentHealth > 0)
       {
           LowHealth?.Invoke();
       }
       ```  
     - В `GameManager`:  
       ```csharp
       void OnEnable()
       {
           PlayerController.LowHealth += OnLowHealth;
       }

       void OnDisable()
       {
           PlayerController.LowHealth -= OnLowHealth;
       }

       private void OnLowHealth()
       {
           Debug.Log("Событие: Низкое здоровье игрока!");
       }
       ```  
   - Проверь: сообщение при здоровье ≤ 30!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если события не срабатывают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь подписку/отписку в `OnEnable`/`OnDisable`.  
  - Используется ли `?.Invoke()`?  
- **Хочешь эпичности?** В `OnPlayerDeath` добавь:  
  ```csharp
  Debug.Log("Событие: Конец игры!");
  ```  
  - Увидишь сообщение в **Console**!  
- **События не работают?** Проверь сигнатуры делегатов.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
События делают игру модульной, позволяя скриптам реагировать на действия без прямой зависимости. Это ключ к гибкости!

## Заключение: События — Твои Сигналы Боя! 💻
Ты освоил делегаты и события, наладив связь между скриптами. Твой шутер стал модульным! Следующий шаг — сериализация для настроек в Inspector. Продолжай, связист кода!

**Что Далее?**  
- Перейди к [Сериализация — Настройки в Inspector](../Advanced/Lesson19_Serialization.md) — настрой игру.  
- Вопросы: [Unity Learn: Events](https://learn.unity.com/tutorial/events).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
