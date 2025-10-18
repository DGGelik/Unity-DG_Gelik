
# 🌐 Урок 17: Singleton — Один Менеджер для Игры

Привет, юный координатор арены! 💻 Добро пожаловать на четвёртый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **паттерн Singleton** в C#, чтобы создать единый менеджер для управления игрой в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Singleton обеспечивает единственный экземпляр класса, доступный из любой части игры. Ты централизуешь управление очками и состоянием игры! Готов стать главным координатором? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 16: Object Pooling](../Advanced/Lesson16_Pooling.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя штаб-квартира управления!  

**Предупреждение**: Singleton требует осторожности, чтобы избежать дублирования объектов. Сохраняй код (**Ctrl+S**) перед тестом, иначе менеджер может не работать! Если очки или состояние не обновляются, проверь Singleton в `Awake`. Врубай Play Mode и управляй игрой!

Готов централизовать управление? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Singleton?**
**Singleton** — это паттерн проектирования, который гарантирует, что у класса есть только один экземпляр, доступный глобально.  
- **Единственный экземпляр**: Создаётся в `Awake` с `DontDestroyOnLoad`.  
- **Статическое свойство**: Доступ через `Instance`.  

В твоём шутере `GameManager` станет Singleton, управляющим очками и состоянием игры.

*Ссылка на документацию*: [Design Patterns](https://learn.unity.com/tutorial/design-patterns).

## 🔄 **Зачем это Нужно?**
Singleton упрощает доступ к глобальным данным, таким как очки или состояние игры. В этом квесте ты:  
- Улучшишь `GameManager` как Singleton.  
- Перенесёшь управление очками из `PlayerController` в `GameManager`.  
- Сделаешь игру управляемой и организованной!  

**Почему это круто?**  
- **Доступность**: Лёгкий доступ к менеджеру из любого скрипта.  
- **Централизация**: Все данные в одном месте.  
- **Для шутера**: Упрощает управление прогрессом и статистикой!  

**Типичные ошибки новичков**:  
- Забыл `DontDestroyOnLoad` для сохранения Singleton.  
- Создание нескольких экземпляров из-за ошибок в `Awake`.  
- Неправильный доступ к `Instance`.  

## ⚙️ **Квест: Централизуй управление**

### Уровень 1: Улучши `GameManager`  
1. **Обнови `GameManager`:**  
   - Перенеси управление очками:  
     ```csharp
     [SerializeField] private TextMeshProUGUI scoreText;
     private int totalScore = 0;

     public void AddScore(int score)
     {
         totalScore += score;
         scoreText.text = "Очки: " + totalScore;
         Debug.Log($"Очки добавлены: {score}, всего: {totalScore}");
     }
     ```

2. **Полный код `GameManager`:**  
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

       void Update()
       {
           statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}";
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
   }
   ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `GameManager`, перетащи `ScoreText` в поле `Score Text`.  
   - Нажми **Play** — очки отображаются через `GameManager`!  

### Уровень 2: Обнови `PlayerController`  
1. **Перенеси управление очками:**  
   - Удали `totalScore` и `scoreText` из `PlayerController`.  
   - В `TakeDamage` и `ShootWithReload` используй `GameManager`:  
     ```csharp
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
     ```

2. **Полный код `PlayerController`:**  
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

### Уровень 3: Добавь перезапуск игры  
1. **Обнови `GameManager`:**  
   - Добавь метод перезапуска:  
     ```csharp
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
     ```

2. **Обнови `PlayerController`:**  
   - Добавь вызов перезапуска по клавише `R`:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.R))
     {
         GameManager.Instance.RestartGame();
     }
     ```

3. **Настрой и протестируй:**  
   - Нажми **Play**, нажми `R` — игра перезапускается, очки сбрасываются, враги и бонусы исчезают!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Score Text` и `Stats Text` в `GameManager`.  
- В **Scene/Game**: Очки обновляются через `GameManager`, перезапуск по `R`.  
- В **Console**: Сообщения о добавлении очков и перезапуске.  

## 💡 **Квесты: Прокачай управление!**
1. **Базовый квест**:  
   - В `GameManager` увеличь начальные очки до 100:  
     ```csharp
     private int totalScore = 100;
     ```  
   - Проверь: игра начинается с 100 очков!  

2. **Квест на перезапуск**:  
   - В `RestartGame` добавь сообщение:  
     ```csharp
     Debug.Log("Игра перезапущена! Очки: " + totalScore);
     ```  
   - Проверь: сообщение в **Console**!  

3. **Квест на UI**:  
   - В `StatsText` добавь отображение очков:  
     ```csharp
     statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}, Очки: {totalScore}";
     ```  
   - Проверь: очки в UI!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь в `GameManager` метод паузы:  
     ```csharp
     public void TogglePause()
     {
         Time.timeScale = Time.timeScale == 0 ? 1 : 0;
         Debug.Log($"Игра {(Time.timeScale == 0 ? "на паузе" : "возобновлена")}");
     }
     ```  
   - В `PlayerController` вызови по `P`:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.P))
     {
         GameManager.Instance.TogglePause();
     }
     ```  
   - Проверь: пауза по `P`!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если Singleton не работает:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь `Awake` и `DontDestroyOnLoad`.  
  - Правильные ли ссылки в **Inspector**?  
- **Хочешь эпичности?** В `GameManager` добавь:  
  ```csharp
  Debug.Log($"Текущий менеджер: {Instance.name}");
  ```  
  - Увидишь Singleton в **Console**!  
- **Очки не обновляются?** Проверь вызовы `AddScore`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Singleton централизует управление, упрощая доступ к данным и функциям. Это ключ к организованной игре!

## Заключение: Singleton — Твой Главный Координатор! 💻
Ты освоил Singleton, централизовав управление очками. Твой шутер стал организованным! Следующий шаг — делегаты для сигналов. Продолжай, координатор кода!

**Что Далее?**  
- Перейди к [Делегаты и Events — Кастомные Сигналы](../Advanced/Lesson18_Delegates.md) — создай события.  
- Вопросы: [Unity Learn: Design Patterns](https://learn.unity.com/tutorial/design-patterns).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
