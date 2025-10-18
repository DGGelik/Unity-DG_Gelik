
# ⏲️ Урок 21: Time и DeltaTime — Плавные Движения

Привет, юный хрономастер арены! 💻 Добро пожаловать на восьмой уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **Time** и **DeltaTime** в Unity, чтобы сделать движения и анимации в твоём топ-даун шутере (*UnityTopDownShooterQuest*) плавными и независимыми от частоты кадров. Ты добавишь плавное движение игрока и врагов, а также таймер для волн спавна! Готов управлять временем? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 20: Mathf и Random](../Advanced/Lesson20_MathRandom.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя машина времени!  

**Предупреждение**: Time и DeltaTime требуют правильного умножения на `Time.deltaTime` для плавности. Сохраняй код (**Ctrl+S**) перед тестом, иначе движения будут рваными! Если движения дёргаются, проверь умножение на `Time.deltaTime`. Врубай Play Mode и управляй временем!

Готов сделать игру плавной? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Time и DeltaTime?**
**Time** в Unity управляет временем в игре, а **Time.deltaTime** — это время между кадрами.  
- **Time.deltaTime**: Делает движение независимым от частоты кадров.  
- **Time.time**: Время с начала сцены, полезно для анимаций.  
- **Time.timeScale**: Управляет скоростью игры (например, для паузы).  

В твоём шутере ты сделаешь движения плавными и добавишь таймер волн.

*Ссылка на документацию*: [Time](https://docs.unity3d.com/ScriptReference/Time.html).

## 🔄 **Зачем это Нужно?**
Time и DeltaTime обеспечивают плавность движений и синхронизацию событий. В этом квесте ты:  
- Сделаешь движение игрока и врагов плавным.  
- Добавишь волны спавна врагов с таймером.  
- Сделаешь игру стабильной и профессиональной!  

**Почему это круто?**  
- **Плавность**: Движения выглядят естественно.  
- **Контроль**: Таймеры для волн добавляют структуру.  
- **Для шутера**: Плавный геймплей улучшает опыт!  

**Типичные ошибки новичков**:  
- Забыл умножить на `Time.deltaTime` в `Update`.  
- Неправильное использование `Time.time` для анимаций.  
- Игнорирование `Time.timeScale` при паузе.  

## ⚙️ **Квест: Сделай игру плавной**

### Уровень 1: Плавное движение игрока  
1. **Проверь `PlayerController`:**  
   - Убедись, что движение уже использует `Time.deltaTime`:  
     ```csharp
     transform.Translate(movement * moveSpeed * Time.deltaTime);
     ```

2. **Добавь плавное вращение:**  
   - В `Update` добавь вращение к курсору:  
     ```csharp
     if (hit.collider != null)
     {
         Vector3 lookDirection = (hit.point - transform.position).normalized;
         transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 5.0f);
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
   - Убедись, что `Player` настроен правильно.  
   - Нажми **Play** — игрок плавно поворачивается к курсору!  

### Уровень 2: Плавное движение врагов  
1. **Обнови `NormalEnemy`:**  
   - Убедись, что движение использует `Time.deltaTime`:  
     ```csharp
     transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
     ```

2. **Обнови `BossEnemy`:**  
   - Добавь плавное ускорение:  
     ```csharp
     private float speedMultiplier = 1.0f;

     void Update()
     {
         if (player != null)
         {
             Vector3 direction = (player.position - transform.position).normalized;
             speedMultiplier = Mathf.Lerp(speedMultiplier, 1.5f, Time.deltaTime * 0.5f);
             transform.Translate(direction * moveSpeed * speedMultiplier * Time.deltaTime);
         }
     }
     ```

3. **Полный код `BossEnemy`:**  
   ```csharp
   using UnityEngine;

   public class BossEnemy : Enemy
   {
       [SerializeField] private Transform player;
       [SerializeField] private int damage = 20;
       private float speedMultiplier = 1.0f;

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
               speedMultiplier = Mathf.Lerp(speedMultiplier, 1.5f, Time.deltaTime * 0.5f);
               transform.Translate(direction * moveSpeed * speedMultiplier * Time.deltaTime);
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
   - Убедись, что `Boss.prefab` настроен.  
   - Нажми **Play** — боссы ускоряются плавно!  

### Уровень 3: Таймер волн спавна  
1. **Обнови `EnemySpawner`:**  
   - Добавь волны с таймером:  
     ```csharp
     [SerializeField] private float waveInterval = 10.0f;
     private float waveTimer = 0f;
     private int waveNumber = 1;

     void Update()
     {
         waveTimer += Time.deltaTime;
         if (waveTimer >= waveInterval)
         {
             waveNumber++;
             spawnInterval = Mathf.Max(0.5f, spawnInterval * 0.9f);
             Debug.Log($"Новая волна {waveNumber}! Интервал спавна: {spawnInterval}");
             waveTimer = 0f;
         }
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
       [SerializeField] private float waveInterval = 10.0f;
       private float waveTimer = 0f;
       private int waveNumber = 1;

       void Start()
       {
           StartCoroutine(SpawnRoutine());
       }

       void Update()
       {
           waveTimer += Time.deltaTime;
           if (waveTimer >= waveInterval)
           {
               waveNumber++;
               spawnInterval = Mathf.Max(0.5f, spawnInterval * 0.9f);
               Debug.Log($"Новая волна {waveNumber}! Интервал спавна: {spawnInterval}");
               waveTimer = 0f;
           }
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
   - В **Hierarchy** выбери `EnemySpawner`, установи `Wave Interval` на 8.0.  
   - Нажми **Play** — волны сменяются каждые 8 секунд, враги появляются чаще!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для настройки интервалов и скоростей.  
- В **Scene/Game**: Плавное движение игрока и врагов, волны спавна.  
- В **Console**: Сообщения о волнах и параметрах.  

## 💡 **Квесты: Прокачай время!**
1. **Базовый квест**:  
   - В `PlayerController` увеличь скорость вращения до 10.0f:  
     ```csharp
     transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(lookDirection), Time.deltaTime * 10.0f);
     ```  
   - Проверь: игрок поворачивается быстрее!  

2. **Квест на волны**:  
   - В `EnemySpawner` уменьши `Wave Interval` до 5.0.  
   - Проверь: волны сменяются быстрее!  

3. **Квест на врагов**:  
   - В `BossEnemy` увеличь максимальный `speedMultiplier` до 2.0f.  
   - Проверь: боссы ускоряются сильнее!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь в `GameManager` отображение времени волны:  
     ```csharp
     statsText.text += $"\nВолна: {waveNumber}, Таймер: {waveTimer:F1}";
     ```  
   - В `EnemySpawner` сделай `waveNumber` и `waveTimer` публичными:  
     ```csharp
     public int waveNumber = 1;
     public float waveTimer = 0f;
     ```  
   - Проверь: время волны в UI!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если движения дёргаются:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь умножение на `Time.deltaTime`.  
  - Учитывается ли `Time.timeScale`?  
- **Хочешь эпичности?** В `EnemySpawner` добавь:  
  ```csharp
  Debug.Log($"Время волны: {waveTimer:F1}");
  ```  
  - Увидишь таймер в **Console**!  
- **Волны не сменяются?** Проверь `waveInterval`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Time и DeltaTime делают движения плавными и синхронизируют события, улучшая качество игры. Это ключ к профессиональному геймплею!

## Заключение: Time — Твой Хрономастер Боя! 💻
Ты освоил Time и DeltaTime, сделав игру плавной. Твой шутер стал стабильным! Следующий шаг — сохранение прогресса с PlayerPrefs. Продолжай, хрономастер кода!

**Что Далее?**  
- Перейди к [PlayerPrefs — Сохранение Прогресса](../Advanced/Lesson22_Saving.md) — сохрани прогресс.  
- Вопросы: [Unity Learn: Time](https://learn.unity.com/tutorial/time).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
