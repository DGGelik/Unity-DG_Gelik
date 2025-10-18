
# 🌐 Урок 30: Сетевой Код — Основы Мультиплеера

Привет, юный сетевой мастер арены! 💻 Добро пожаловать на финальный уровень *Части 5* твоего квеста *UnityCSQuest*! Сегодня ты освоишь основы **сетевого кода** в Unity с использованием **Mirror**, чтобы добавить мультиплеер в твой топ-даун шутер (*UnityTopDownShooterQuest*). Ты сделаешь игрока сетевым и синхронизируешь его движение! Готов объединить игроков? Время на квест: 30–35 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool` из [Урок 29: Оптимизация](../Master/Lesson29_Optimization.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- **Mirror** (установи через **Package Manager**).  
- Папка Scripts в Assets — твоя сетевая станция!  

**Предупреждение**: Сетевой код требует настройки **NetworkManager** и синхронизации. Сохраняй код (**Ctrl+S**) и префабы перед тестом, иначе мультиплеер не запустится! Если игроки не синхронизируются, проверь **NetworkIdentity** и **ClientRpc**. Врубай Play Mode и объединяй игроков!

Готов создать мультиплеер? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Сетевой Код?**
**Сетевой код** в Unity позволяет игрокам взаимодействовать через интернет или локальную сеть.  
- **Mirror**: Библиотека для мультиплеера, упрощающая синхронизацию.  
- **NetworkBehaviour**: Базовый класс для сетевых скриптов.  
- **SyncVar**: Синхронизирует переменные между клиентами.  

В твоём шутере ты сделаешь игрока сетевым с синхронизацией движения.

*Ссылка на документацию*: [Mirror](https://mirror-networking.com/docs/).

## 🔄 **Зачем это Нужно?**
Мультиплеер делает игру социальной и увлекательной. В этом квесте ты:  
- Настроишь **NetworkManager** для подключения.  
- Синхронизируешь движение и здоровье игрока.  
- Сделаешь игру кооперативной!  

**Почему это круто?**  
- **Социальность**: Игра с друзьями.  
- **Динамика**: Синхронизированные действия.  
- **Для шутера**: Кооперативные бои — это эпично!  

**Типичные ошибки новичков**:  
- Отсутствие **NetworkIdentity** на префабах.  
- Неправильное использование **Command**/**ClientRpc**.  
- Незапущенный хост перед подключением клиента.  

## ⚙️ **Квест: Создай мультиплеер**

### Подготовка: Настрой Mirror  
1. **Установи Mirror:**  
   - В **Package Manager** найди и установи **Mirror**.  
   - В **Hierarchy** создай пустой объект `NetworkManager`, добавь компоненты `NetworkManager` и `NetworkManagerHUD`.  

2. **Настрой `Player.prefab`:**  
   - Добавь компонент **NetworkIdentity** (установи `Local Player Authority`).  
   - В **NetworkManager** перетащи `Player.prefab` в поле `Player Prefab`.  

### Уровень 1: Сетевое движение игрока  
1. **Обнови `PlayerController`:**  
   - Замени MonoBehaviour на NetworkBehaviour:  
     ```csharp
     using Mirror;
     using System.Collections;
     using UnityEngine;
     using TMPro;

     public class PlayerController : NetworkBehaviour
     {
         [SyncVar] private int currentHealth;
         [SyncVar] private int currentAmmo;
         [SyncVar] private Vector3 syncPosition;
         [SyncVar] private Quaternion syncRotation;
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
         [SerializeField] private Material flashMaterial;
         private Material originalMaterial;
         private Renderer playerRenderer;
         private bool canShoot = true;
         private Camera mainCamera;
         private Animator animator;
         private AudioSource audioSource;

         void Start()
         {
             if (isLocalPlayer)
             {
                 currentHealth = maxHealth;
                 currentAmmo = maxAmmo;
                 mainCamera = Camera.main;
                 animator = GetComponent<Animator>();
                 audioSource = GetComponent<AudioSource>();
                 playerRenderer = GetComponent<Renderer>();
                 originalMaterial = playerRenderer.material;
                 healthBar.maxValue = maxHealth;
                 healthBar.value = currentHealth;
                 Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
                 TakeDamage(60);
             }
         }

         void Update()
         {
             if (!isLocalPlayer) return;

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

             CmdSyncTransform(transform.position, transform.rotation);
         }

         [Command]
         void CmdSyncTransform(Vector3 position, Quaternion rotation)
         {
             syncPosition = position;
             syncRotation = rotation;
             RpcUpdateTransform(position, rotation);
         }

         [ClientRpc]
         void RpcUpdateTransform(Vector3 position, Quaternion rotation)
         {
             if (!isLocalPlayer)
             {
                 transform.position = position;
                 transform.rotation = rotation;
             }
         }

         public void TakeDamage(int damage)
         {
             if (!isServer) return;
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
                 EventPool.Instance.TriggerEvent("PlayerDeath");
                 RpcDestroyPlayer();
             }
             else
             {
                 StartCoroutine(FlashEffect());
             }
         }

         [ClientRpc]
         void RpcDestroyPlayer()
         {
             Destroy(gameObject);
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
                 CmdShoot(direction);
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

         [Command]
         void CmdShoot(Vector3 direction)
         {
             for (int i = 0; i < bulletsPerShot; i++)
             {
                 GameObject bullet = BulletPool.Instance.GetBullet();
                 bullet.transform.position = transform.position + direction * 0.5f;
                 bullet.transform.rotation = Quaternion.identity;
                 bullet.SetActive(true);
                 bullet.GetComponent<BulletController>().Shoot(direction, bulletSpeed);
                 NetworkServer.Spawn(bullet);
                 Debug.Log(playerName + " выстрелил пулей " + (i + 1) + " в сторону!");
             }
         }

         private IEnumerator FlashEffect()
         {
             playerRenderer.material = flashMaterial;
             flashMaterial.SetFloat("_FlashIntensity", 1.0f);
             yield return new WaitForSeconds(0.5f);
             flashMaterial.SetFloat("_FlashIntensity", 0.0f);
             playerRenderer.material = originalMaterial;
         }
     }
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `NetworkManager`, запусти хост (`Start Host` в HUD).  
   - В другой сцене запусти клиент (`Start Client`).  
   - Нажми **Play** — игроки синхронизируют движение!  

### Уровень 2: Синхронизация здоровья  
1. **Обнови `PlayerController`:**  
   - Здоровье уже синхронизировано через `[SyncVar]`.  
   - Обнови `healthBar` для клиентов:  
     ```csharp
     void Update()
     {
         if (!isLocalPlayer)
         {
             healthBar.value = currentHealth;
         }
         // ... остальной код ...
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, получи урон на одном клиенте — здоровье синхронизируется!  

### Уровень 3: Сетевая камера  
1. **Обнови `CameraFollow`:**  
   - Добавь сетевую проверку:  
     ```csharp
     using Mirror;
     using System.Collections;
     using UnityEngine;

     public class CameraFollow : NetworkBehaviour
     {
         [SerializeField] private Transform target;
         [SerializeField] private Vector3 offset = new Vector3(0, 10, -10);
         [SerializeField] private float smoothSpeed = 0.125f;
         [SerializeField] private Vector2 minBounds = new Vector2(-20, -20);
         [SerializeField] private Vector2 maxBounds = new Vector2(20, 20);
         private Vector3 velocity;

         void Start()
         {
             if (isLocalPlayer)
             {
                 target = transform;
             }
         }

         void LateUpdate()
         {
             if (target != null)
             {
                 Vector3 desiredPosition = target.position + offset;
                 Vector3 smoothedPosition = Vector3.SmoothDamp(transform.position, desiredPosition, ref velocity, smoothSpeed);
                 smoothedPosition.x = Mathf.Clamp(smoothedPosition.x, minBounds.x, maxBounds.x);
                 smoothedPosition.z = Mathf.Clamp(smoothedPosition.z, minBounds.y, maxBounds.y);
                 transform.position = smoothedPosition;
                 transform.LookAt(target);
             }
         }

         public void Shake(float duration, float magnitude)
         {
             StartCoroutine(ShakeCoroutine(duration, magnitude));
         }

         private IEnumerator ShakeCoroutine(float duration, float magnitude)
         {
             Vector3 originalPos = transform.position;
             float elapsed = 0f;

             while (elapsed < duration)
             {
                 float x = Random.Range(-1f, 1f) * magnitude;
                 float z = Random.Range(-1f, 1f) * magnitude;
                 transform.position += new Vector3(x, 0, z);
                 elapsed += Time.deltaTime;
                 yield return null;
             }
             transform.position = Vector3.Lerp(transform.position, originalPos, Time.deltaTime);
         }
     }
     ```

2. **Настрой `Main Camera`:**  
   - Добавь **NetworkIdentity** на `Main Camera`.  
   - Перетащи `Player` в поле `Target` для локального игрока.  

3. **Настрой и протестируй:**  
   - Нажми **Play**, запусти хост и клиент — камера следует за каждым игроком!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля в `NetworkManager` и `PlayerController`.  
- В **Scene/Game**: Игроки синхронизируют движение и здоровье.  
- В **Console**: Сообщения о сетевых действиях.  

## 💡 **Квесты: Прокачай мультиплеер!**
1. **Базовый квест**:  
   - Увеличь `moveSpeed` в `PlayerController` до 7.0f.  
   - Проверь: игроки двигаются быстрее!  

2. **Квест на здоровье**:  
   - В `PlayerController` добавь отображение имени:  
     ```csharp
     [SyncVar] private string syncPlayerName;

     void Start()
     {
         if (isLocalPlayer)
         {
             syncPlayerName = playerName;
         }
         // ... остальной код ...
     }
     ```  
   - Проверь: имена синхронизируются!  

3. **Квест на камеру**:  
   - Увеличь `smoothSpeed` в `CameraFollow` до 0.2f.  
   - Проверь: камера следует быстрее!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Синхронизируй стрельбу врагов:  
     - В `EnemySpawner`:  
       ```csharp
       [Command]
       void CmdSpawnEnemy()
       {
           GameObject prefabToSpawn = Random.value < bossSpawnChance ? bossEnemyPrefab : normalEnemyPrefab;
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
   - Проверь: враги появляются на всех клиентах!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если мультиплеер не работает:  
  - Сохранил ли код и префабы (**Ctrl+S**)?  
  - Проверь **NetworkIdentity** и **NetworkManager**.  
  - Запущен ли хост?  
- **Хочешь эпичности?** В `CmdShoot` добавь:  
  ```csharp
  Debug.Log("Сетевая стрельба!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Синхронизация не работает?** Проверь `[SyncVar]` и **ClientRpc**.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Мультиплеер делает игру социальной, позволяя играть с друзьями. Это ключ к кооперативному веселью!

## Заключение: Сетевой Код — Твой Портал в Мультиплеер! 💻
Ты освоил основы мультиплеера, добавив сетевых игроков. Твой шутер стал кооперативным! Это финал *Части 5*! Поздравляю, ты достиг мастерского уровня! Теперь создай свою игру или улучшай *UnityTopDownShooterQuest* с новыми идеями!

**Что Далее?**  
- Создай новую игру или добавь функции (рейтинги, чат, новые уровни)!  
- Вопросы: [Mirror Documentation](https://mirror-networking.com/docs/).  

[Назад к оглавлению](../CS_Unity.md)  

*Author: [DGGelik](https://github.com/DGGelik). Date: October 18, 2025.*
