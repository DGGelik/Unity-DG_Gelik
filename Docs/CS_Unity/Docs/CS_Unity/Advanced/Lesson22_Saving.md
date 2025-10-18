
# 💾 Урок 22: PlayerPrefs — Сохранение Прогресса

Привет, юный хранитель арены! 💻 Добро пожаловать на девятый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **PlayerPrefs** в Unity, чтобы сохранять прогресс игрока в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты сохранишь очки и волну, чтобы игрок мог продолжить с того же места! Готов сохранить свой прогресс? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 21: Time](../Advanced/Lesson21_Time.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя база данных прогресса!  

**Предупреждение**: PlayerPrefs требует правильных ключей и типов данных. Сохраняй код (**Ctrl+S**) перед тестом, иначе данные не сохранятся! Если прогресс не загружается, проверь ключи и вызовы `PlayerPrefs.Save()`. Врубай Play Mode и сохраняй игру!

Готов сохранить прогресс? Погнали по уровням квеста! 🚀

## 🎯 **Что такое PlayerPrefs?**
**PlayerPrefs** — это простой способ сохранения данных в Unity (очки, настройки, прогресс).  
- **Ключи**: Строки для идентификации данных (например, `"TotalScore"`).  
- **Типы данных**: Поддерживает `int`, `float`, `string`.  
- **Сохранение/загрузка**: Методы `PlayerPrefs.SetInt`, `PlayerPrefs.GetInt`, и т.д.  

В твоём шутере ты сохранишь очки и номер волны.

*Ссылка на документацию*: [PlayerPrefs](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html).

## 🔄 **Зачем это Нужно?**
PlayerPrefs сохраняет прогресс, чтобы игрок не терял достижения. В этом квесте ты:  
- Сохранишь очки и волну в `GameManager`.  
- Загрузишь прогресс при старте игры.  
- Сделаешь игру запоминающей!  

**Почему это круто?**  
- **Прогресс**: Игрок продолжает с последнего достижения.  
- **Удобство**: Простое сохранение без сложных систем.  
- **Для шутера**: Очки и волны сохраняют интерес!  

**Типичные ошибки новичков**:  
- Неправильные ключи в `PlayerPrefs`.  
- Забыл вызвать `PlayerPrefs.Save()`.  
- Неправильный тип данных при загрузке.  

## ⚙️ **Квест: Сохрани прогресс**

### Уровень 1: Сохранение очков  
1. **Обнови `GameManager`:**  
   - Добавь сохранение и загрузку очков:  
     ```csharp
     void Start()
     {
         totalScore = PlayerPrefs.GetInt("TotalScore", 0);
         scoreText.text = "Очки: " + totalScore;
     }

     public void AddScore(int score)
     {
         totalScore += score;
         scoreText.text = "Очки: " + totalScore;
         PlayerPrefs.SetInt("TotalScore", totalScore);
         PlayerPrefs.Save();
         Debug.Log($"Очки добавлены: {score}, всего: {totalScore}");
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, набери очки, выйди из режима.  
   - Запусти снова — очки сохраняются!  

### Уровень 2: Сохранение волны  
1. **Обнови `EnemySpawner`:**  
   - Сделай `waveNumber` доступным:  
     ```csharp
     public int GetWaveNumber() => waveNumber;
     public void SetWaveNumber(int wave) => waveNumber = wave;
     ```

2. **Обнови `GameManager`:**  
   - Добавь сохранение и загрузку волны:  
     ```csharp
     [SerializeField] private EnemySpawner enemySpawner;

     void Start()
     {
         totalScore = PlayerPrefs.GetInt("TotalScore", 0);
         scoreText.text = "Очки: " + totalScore;
         int savedWave = PlayerPrefs.GetInt("WaveNumber", 1);
         enemySpawner.SetWaveNumber(savedWave);
     }

     public void RestartGame()
     {
         totalScore = 0;
         scoreText.text = "Очки: " + totalScore;
         PlayerPrefs.SetInt("TotalScore", totalScore);
         enemySpawner.SetWaveNumber(1);
         PlayerPrefs.SetInt("WaveNumber", 1);
         foreach (var enemy in enemies.ToArray())
         {
             enemy.TakeDamage(1000);
         }
         foreach (var bonus in bonuses.ToArray())
         {
             Destroy(bonus.gameObject);
         }
         PlayerPrefs.Save();
         Debug.Log("Игра перезапущена!");
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
       [SerializeField] private TextMeshProUGUI scoreText;
       [SerializeField] private TextMeshProUGUI statsText;
       [SerializeField] private EnemySpawner enemySpawner;
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
           HealthBonus.BonusCollected += OnBonusCollected;
       }

       void OnDisable()
       {
           PlayerController.PlayerDied -= OnPlayerDeath;
           Enemy.EnemyDied -= OnEnemyDeath;
           HealthBonus.BonusCollected -= OnBonusCollected;
       }

       void Start()
       {
           totalScore = PlayerPrefs.GetInt("TotalScore", 0);
           scoreText.text = "Очки: " + totalScore;
           int savedWave = PlayerPrefs.GetInt("WaveNumber", 1);
           enemySpawner.SetWaveNumber(savedWave);
       }

       void Update()
       {
           statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}, Очки: {totalScore}, Волна: {enemySpawner.GetWaveNumber()}";
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
           PlayerPrefs.SetInt("TotalScore", totalScore);
           PlayerPrefs.Save();
           Debug.Log($"Очки добавлены: {score}, всего: {totalScore}");
       }

       public void RestartGame()
       {
           totalScore = 0;
           scoreText.text = "Очки: " + totalScore;
           PlayerPrefs.SetInt("TotalScore", totalScore);
           enemySpawner.SetWaveNumber(1);
           PlayerPrefs.SetInt("WaveNumber", 1);
           foreach (var enemy in enemies.ToArray())
           {
               enemy.TakeDamage(1000);
           }
           foreach (var bonus in bonuses.ToArray())
           {
               Destroy(bonus.gameObject);
           }
           PlayerPrefs.Save();
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
           PlayerPrefs.SetInt("WaveNumber", enemySpawner.GetWaveNumber());
           PlayerPrefs.Save();
       }

       private void OnBonusCollected(HealthBonus bonus)
       {
           AddScore(20);
           Debug.Log("Событие: Бонус собран, добавлено 20 очков!");
       }
   }
   ```

4. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `GameManager`, перетащи `EnemySpawner` в поле `Enemy Spawner`.  
   - Нажми **Play**, пройди несколько волн, выйди — волна сохраняется!  

### Уровень 3: Очистка сохранений  
1. **Обнови `GameManager`:**  
   - Добавь метод очистки:  
     ```csharp
     public void ClearSave()
     {
         PlayerPrefs.DeleteAll();
         totalScore = 0;
         scoreText.text = "Очки: " + totalScore;
         enemySpawner.SetWaveNumber(1);
         PlayerPrefs.SetInt("WaveNumber", 1);
         PlayerPrefs.Save();
         Debug.Log("Сохранения очищены!");
     }
     ```

2. **Обнови `PlayerController`:**  
   - Добавь вызов очистки по `C`:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.C))
     {
         GameManager.Instance.ClearSave();
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

           Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
           RaycastHit hit;
           if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
           {
               Vector3 lookDirection = (hit.point - transform.position).normalized;
               transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
           }

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

           if (Input.GetKeyDown(KeyCode.C))
           {
               GameManager.Instance.ClearSave();
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
   - Нажми **Play**, набери очки, нажми `C` — сохранения очищаются!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поле `Enemy Spawner` в `GameManager`.  
- В **Scene/Game**: Очки и волна сохраняются между сессиями.  
- В **Console**: Сообщения о сохранении и очистке.  

## 💡 **Квесты: Прокачай сохранения!**
1. **Базовый квест**:  
   - В `GameManager` сохрани `maxHealth` игрока:  
     ```csharp
     PlayerPrefs.SetInt("PlayerHealth", maxHealth);
     ```  
   - Проверь: здоровье сохраняется!  

2. **Квест на волны**:  
   - В `EnemySpawner` сохрани `spawnInterval`:  
     ```csharp
     PlayerPrefs.SetFloat("SpawnInterval", spawnInterval);
     ```  
   - Проверь: интервал сохраняется!  

3. **Квест на UI**:  
   - В `GameManager` отобрази сохранённые очки:  
     ```csharp
     statsText.text += $"\nСохранено: {PlayerPrefs.GetInt("TotalScore", 0)}";
     ```  
   - Проверь: UI показывает сохранённые очки!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Сохрани имя игрока в `PlayerController`:  
     ```csharp
     PlayerPrefs.SetString("PlayerName", playerName);
     playerName = PlayerPrefs.GetString("PlayerName", "Hero");
     ```  
   - Проверь: имя сохраняется между сессиями!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если данные не сохраняются:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь ключи в `PlayerPrefs`.  
  - Вызывается ли `PlayerPrefs.Save()`?  
- **Хочешь эпичности?** В `ClearSave` добавь:  
  ```csharp
  Debug.Log("Все сохранения сброшены!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Данные не загружаются?** Проверь типы данных (`int`, `float`, `string`).  

## ⚙️ **Зачем это пригодится в твоём шутере?**
PlayerPrefs сохраняет прогресс, делая игру удобной для игрока. Это ключ к удержанию интереса!

## Заключение: PlayerPrefs — Твой Хранитель Прогресса! 💻
Ты освоил PlayerPrefs, сохранив очки и волны. Твой шутер стал запоминающим! Следующий шаг — переходы между уровнями со сценами. Продолжай, хранитель кода!

**Что Далее?**  
- Перейди к [Сцены — Переходы между Уровнями](../Advanced/Lesson23_Scenes.md) — создай уровни.  
- Вопросы: [Unity Learn: PlayerPrefs](https://learn.unity.com/tutorial/playerprefs).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
