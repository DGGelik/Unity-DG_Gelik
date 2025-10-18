
# 🎮 Урок 32: New Input System — Современный Ввод

Привет, юный мастер управления! 💻 Добро пожаловать на второй уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **New Input System** в Unity, чтобы модернизировать управление в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты заменишь старый Input на новую систему для гибкого управления! Готов улучшить контроль? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool` из [Урок 31: Отладка](../Additional/Lesson31_Debugging.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- **Input System** (установи через **Package Manager**).  
- Папка Scripts в Assets — твоя панель управления!  

**Предупреждение**: New Input System требует настройки **Input Actions** и привязок. Сохраняй код (**Ctrl+S**) и настройки перед тестом, иначе ввод не сработает! Если действия не выполняются, проверь **Player Input** и **Input Action Asset**. Врубай Play Mode и управляй ареной!

Готов модернизировать ввод? Погнали по уровням квеста! 🚀

## 🎯 **Что такое New Input System?**
**New Input System** — современная система ввода в Unity, заменяющая устаревший `Input.GetAxis`.  
- **Input Actions**: Настраиваемые действия (движение, стрельба).  
- **Player Input**: Компонент для обработки ввода.  
- **Bindings**: Привязка действий к клавишам/контроллерам.  

В твоём шутере ты настроишь движение, стрельбу и другие действия через новую систему.

*Ссылка на документацию*: [Input System](https://docs.unity3d.com/Packages/com.unity.inputsystem@1.0/manual/index.html).

## 🔄 **Зачем это Нужно?**
New Input System делает управление гибким и кроссплатформенным. В этом квесте ты:  
- Заменишь `Input.GetAxis` на **Input Actions**.  
- Настроишь управление для клавиатуры и мыши.  
- Сделаешь игру удобной для всех устройств!  

**Почему это круто?**  
- **Гибкость**: Легко добавить поддержку геймпадов.  
- **Простота**: Централизованное управление вводом.  
- **Для шутера**: Точное и удобное управление!  

**Типичные ошибки новичков**:  
- Неправильная настройка **Input Action Asset**.  
- Отсутствие компонента **Player Input** на объекте.  
- Незаблокированный старый Input в **Project Settings**.  

## ⚙️ **Квест: Модернизируй ввод**

### Подготовка: Настрой Input System  
1. **Установи Input System:**  
   - В **Package Manager** установи **Input System**.  
   - В **Project Settings** → **Player** → **Active Input Handling**, выбери **Input System Package (New)**.  

2. **Создай Input Action Asset:**  
   - В **Project** → `Assets/Input`, создай **Input Action**, назови `PlayerControls`.  
   - Добавь **Action Map** `Player` с действиями:  
     - `Move` (2D Vector, Bindings: WASD/Arrow Keys).  
     - `Shoot` (Button, Binding: Space).  
     - `Aim` (2D Vector, Binding: Mouse Position).  
     - `Restart` (Button, Binding: R).  
     - `Pause` (Button, Binding: P).  
     - `ClearSave` (Button, Binding: C).  
   - Сохрани актив.

### Уровень 1: Настройка движения и прицеливания  
1. **Обнови `PlayerController`:**  
   - Замени ввод на New Input System:  
     ```csharp
     using Mirror;
     using System.Collections;
     using UnityEngine;
     using UnityEngine.InputSystem;

     public class PlayerController : NetworkBehaviour
     {
         [SyncVar] private int currentHealth;
         [SyncVar] private int currentAmmo;
         [SyncVar] private Vector3 syncPosition;
         [SyncVar] private Quaternion syncRotation;
         [SyncVar] private string syncPlayerName;
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
         private Vector2 moveInput;
         private Vector2 aimInput;

         void Start()
         {
             if (isLocalPlayer)
             {
                 syncPlayerName = playerName;
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
             if (!isLocalPlayer)
             {
                 healthBar.value = currentHealth;
                 return;
             }

             Vector3 movement = new Vector3(moveInput.x, 0, moveInput.y);
             transform.Translate(movement * moveSpeed * Time.deltaTime);
             float speed = movement.magnitude;
             animator.SetFloat("Speed", speed);
             Debug.Log($"Игрок движется: {movement}, Скорость: {speed}");

             Ray ray = mainCamera.ScreenPointToRay(aimInput);
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

             CmdSyncTransform(transform.position, transform.rotation);
         }

         public void OnMove(InputAction.CallbackContext context)
         {
             moveInput = context.ReadValue<Vector2>();
         }

         public void OnAim(InputAction.CallbackContext context)
         {
             aimInput = context.ReadValue<Vector2>();
         }

         public void OnShoot(InputAction.CallbackContext context)
         {
             if (context.performed && canShoot && currentAmmo >= bulletsPerShot)
             {
                 StartCoroutine(ShootWithReload());
             }
             else if (context.performed)
             {
                 Debug.Log("Нельзя стрелять! Перезарядка или нет патронов!");
             }
         }

         public void OnRestart(InputAction.CallbackContext context)
         {
             if (context.performed)
             {
                 GameManager.Instance.RestartGame();
             }
         }

         public void OnPause(InputAction.CallbackContext context)
         {
             if (context.performed)
             {
                 GameManager.Instance.TogglePause();
             }
         }

         public void OnClearSave(InputAction.CallbackContext context)
         {
             if (context.performed)
             {
                 GameManager.Instance.ClearSave();
             }
         }

         // ... остальной код (TakeDamage, CmdSyncTransform, RpcUpdateTransform, ShootWithReload, CmdShoot, RpcDestroyPlayer, FlashEffect) ...
     }
     ```

2. **Настрой `Player.prefab`:**  
   - Добавь компонент **Player Input**, выбери `PlayerControls` в поле **Actions**.  
   - Установи **Behavior** на **Invoke Unity Events**.  
   - В **Events** привяжи методы `OnMove`, `OnAim`, `OnShoot`, `OnRestart`, `OnPause`, `OnClearSave` к соответствующим действиям.  

3. **Настрой и протестируй:**  
   - Нажми **Play**, двигай игрока, стреляй — управление работает через New Input System!  

### Уровень 2: Поддержка геймпада  
1. **Обнови `PlayerControls`:**  
   - В **Input Action Asset** добавь привязки для геймпада:  
     - `Move` → Gamepad Left Stick.  
     - `Aim` → Gamepad Right Stick.  
     - `Shoot` → Gamepad Button South (A).  

2. **Настрой и протестируй:**  
   - Подключи геймпад, нажми **Play** — управление работает с геймпада!  

### Уровень 3: Динамическое переключение устройств  
1. **Обнови `PlayerController`:**  
   - Добавь переключение схем управления:  
     ```csharp
     void Start()
     {
         if (isLocalPlayer)
         {
             // ... существующий код ...
             var playerInput = GetComponent<PlayerInput>();
             playerInput.SwitchCurrentControlScheme(InputSystem.devices.Any(d => d is Gamepad) ? "Gamepad" : "Keyboard&Mouse");
         }
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, подключи/отключи геймпад — схема управления переключается!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля **Player Input** и события в `Player.prefab`.  
- В **Scene/Game**: Плавное управление через клавиатуру или геймпад.  
- В **Console**: Логи движения и стрельбы.  

## 💡 **Квесты: Прокачай ввод!**
1. **Базовый квест**:  
   - Измени привязку `Shoot` на мышь (Left Button).  
   - Проверь: стрельба работает с мыши!  

2. **Квест на геймпад**:  
   - Добавь действие `Zoom` (Gamepad Trigger Right).  
   - В `PlayerController`:  
     ```csharp
     public void OnZoom(InputAction.CallbackContext context)
     {
         if (context.performed)
         {
             Camera.main.fieldOfView = Mathf.Lerp(Camera.main.fieldOfView, 50, Time.deltaTime);
         }
     }
     ```  
   - Проверь: геймпад зумирует камеру!  

3. **Квест на схемы**:  
   - Добавь схему `Touch` в `PlayerControls` для сенсорного ввода.  
   - Проверь: управление работает на сенсорных устройствах (в симуляции)!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь UI для отображения текущей схемы:  
     - В `GameManager`:  
       ```csharp
       [SerializeField] private TextMeshProUGUI inputSchemeText;

       void Start()
       {
           // ... существующий код ...
           inputSchemeText.text = "Схема: " + GetComponent<PlayerInput>().currentControlScheme;
       }
       ```  
   - Проверь: UI показывает текущую схему управления!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если ввод не работает:  
  - Сохранил ли код и активы (**Ctrl+S**)?  
  - Проверь **Player Input** и **Input Action Asset**.  
  - Отключён ли старый Input в **Project Settings**?  
- **Хочешь эпичности?** В `OnShoot` добавь:  
  ```csharp
  Debug.Log("Выстрел через New Input System!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Управление не реагирует?** Проверь привязки в **Input Actions**.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
New Input System делает управление гибким и готовым к кроссплатформенности. Это ключ к удобству!

## Заключение: New Input System — Твой Мастер Управления! 💻
Ты освоил New Input System, модернизировав управление. Твой шутер стал удобнее! Следующий шаг — работа с префабами. Продолжай, мастер управления!

**Что Далее?**  
- Перейди к [Работа с Префабами — Повторное Использование](../Additional/Lesson33_Prefabs.md) — упрости создание объектов.  
- Вопросы: [Unity Learn: Input System](https://learn.unity.com/tutorial/input-system).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
