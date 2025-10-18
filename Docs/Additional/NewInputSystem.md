# 🎮 New Input System — Современный Ввод

Привет, контроллер-мастер! 🎮 Эта глава продвинутой теории — твой полный гид по **New Input System в Unity**: от установки пакета до настройки Actions (ввод с клавиш, мыши, геймпада, тача), событий (callback на нажатие) и интеграции с анимациями/физикой. Мы перейдём от старого Input Manager (GetKey) к новому — гибкому, масштабируемому для твоего топ-даун шутера: ремаппинг клавиш, поддержка контроллера, плавный ввод. Это будущее ввода — легко добавить мобильный тач или VR! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 3+).  
- Input System пакет (Window > Package Manager > Input System — Install).  

**Предупреждение**: Новый System — отдельный: старый не работает после установки (выбери в Player Settings > Active Input Handling: Input System Package). Если события не срабатывают — проверь Action Map в Input Actions asset. Тестируй в Standalone (Device Simulator в Package Manager для эмуляции). Переход от старого — мигрируй код постепенно.  

Готов к ремаппингу? По шагам, как по действиям! 📝

## 🎯 **Что Такое New Input System и Зачем Переходить?**
**New Input System** (Input System package) — современная замена Input Manager: asset-based ввод с Actions (группы действий: Move, Shoot), Device Support (клавиатура + геймпад) и Events (callback вместо polling).  

- **Для чего?**  
  - Гибкость: Ремаппинг (WASD → Arrows), мульти-устройства (мышь + контроллер).  
  - Производительность: Events вместо Update() — меньше нагрузки.  
  - Масштаб: Мобильный тач, VR — легко добавить.  

**Сравнение со Старым**:
| Старый Input | Новый System | Преимущество Нового |
|--------------|--------------|---------------------|
| GetKeyDown(KeyCode.Space) | action.triggered | Ремаппинг, события. |
| GetAxis("Horizontal") | action.ReadValue<Vector2>() | Геймпад, тач. |
| Update() polling | Callback (OnPerformed) | Эффективнее CPU. |

В твоём шутере: Action "Shoot" — клик мыши или A на геймпаде, event OnShoot — звук + анимация. Без него — код жёсткий, неудобный для портирования.  

**Как Установить?**: Package Manager > Input System > Install. Перезапусти Unity, мигрируй (Player Settings > Input Handling > Input System).  

*Ссылка на официальную документацию*: [Input System Overview](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest).

## 🔄 **Как Работает New Input System? (Input Actions Asset и Device)**
System — asset-based: создай InputActions (.inputactions), настрой Maps/Actions/Bindings.  

- **Input Actions Asset**: Файл с действиями (Move: Vector2, Shoot: Button).  
- **Player Input Component**: Связь asset с объектом (Send Messages, Unity Events).  
- **Devices**: Keyboard, Mouse, Gamepad — авто-детект.  

**Распространённые Ошибки**:  
- Asset не сгенерирован — кнопка Generate C# Class.  
- Нет PlayerInput — Add Component > Player Input.  
- Конфликт со старым — выключи в Project Settings.  

**Шаговый Процесс**:
1. Правой кнопкой в Project > Create > Input Actions. Назови "PlayerInputActions".  
2. Двойной клик — редактор: Add Action Map "Player".  
3. Add Action "Move" (Value > Vector2), Binding <Keyboard>/wasd.  
4. Add "Shoot" (Button), Binding <Mouse>/leftButton.  
5. Save Asset > Generate C# Class (внизу).  
6. Add Component > Player Input к герою, Actions = PlayerInputActions. Behavior = Invoke Unity Events.  

**Пример в Шутере: Настройка Move Action**:
В InputActions: Move — Up/Down/Left/Right bindings (W/S/A/D).  

Код (прикрепи к герою):
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class NewInputMove : MonoBehaviour
{
    public float speed = 5f;
    private Vector2 moveInput;

    // Unity Event от Player Input
    public void OnMove(InputAction.CallbackContext context)
    {
        moveInput = context.ReadValue<Vector2>();  // -1 to 1
    }

    void Update()
    {
        Vector3 movement = new Vector3(moveInput.x, 0, moveInput.y) * speed * Time.deltaTime;
        transform.Translate(movement, Space.World);
    }
}
```
*Применение*: Player Input вызывает OnMove — герой двигается по вводу.

*Ссылка на документацию*: [Input Actions](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest/index.html?subfolder=/manual/Action.html).

## ⚙️ **Свойства и Методы Input System**
Asset — дерево: Maps > Actions > Bindings. PlayerInput — мост.

| Компонент | Свойство/Метод | Описание | Пример в Шутере |
|-----------|----------------|----------|-----------------|
| **InputAction** | ReadValue<T>() | Значение (Vector2 для Move). | moveValue = action.ReadValue<Vector2>(); |
| **InputAction** | triggered/performed | События (нажато/выполнено). | if (shootAction.triggered) Shoot(); |
| **PlayerInput** | actions | Доступ к asset. | actions["Shoot"].performed += ctx => Shoot(); |
| **InputDevice** | devices | Устройства. | if (Gamepad.current != null) UseController(); |
| **CallbackContext** | ReadValue<T>() | В event. | Vector2 input = context.ReadValue<Vector2>(); |

**Код: Ремаппинг и Мульти-Device**:
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class RemapInput : MonoBehaviour
{
    private PlayerInput playerInput;

    void Start()
    {
        playerInput = GetComponent<PlayerInput>();
        // Ремаппинг в runtime (продвинутый)
        var shootAction = playerInput.actions["Shoot"];
        shootAction.AddBinding("<Gamepad>/buttonSouth");  // A на геймпаде
    }

    // Event для Shoot
    public void OnShoot(InputAction.CallbackContext context)
    {
        if (context.performed)
        {
            bool buttonValue = context.ReadValueAsButton();  // true/false
            if (buttonValue) Shoot();
        }
    }

    void Shoot()
    {
        Debug.Log("Fired! Device: " + Gamepad.current?.name ?? "Keyboard/Mouse");
    }

    // Отписка
    void OnDestroy()
    {
        playerInput.actions["Shoot"].performed -= OnShoot;
    }
}
```
*Применение*: Авто-детект геймпада — Shoot на A.

*Ссылка на документацию*: [Callbacks](https://docs.unity3d.com/Packages/com.unity.inputsystem@latest/index.html?subfolder=/manual/Callbacks.html).

## 💻 **Практические Примеры New Input System в Твоём Шутере**
Готовый код — мигрируй от старого Input.

#### **Пример 1: Полный Player Input (Move + Shoot + Pause)**
Создай InputActions: Map "Gameplay" — Move (Vector2), Shoot (Button), Pause (Button).  

Код (PlayerInput Component на герое, Behavior = Send Messages):
```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class FullInputHandler : MonoBehaviour
{
    public float moveSpeed = 5f;
    private Vector2 moveVector;

    // Events от Player Input
    public void OnMove(InputAction.CallbackContext context)
    {
        moveVector = context.ReadValue<Vector2>();
    }

    public void OnShoot(InputAction.CallbackContext context)
    {
        if (context.performed)
        {
            Shoot();  // Твой метод
        }
    }

    public void OnPause(InputAction.CallbackContext context)
    {
        if (context.performed)
        {
            TogglePause();  // Пауза
        }
    }

    void Update()
    {
        // Движение на основе ввода
        Vector3 movement = new Vector3(moveVector.x, 0, moveVector.y) * moveSpeed * Time.deltaTime;
        transform.Translate(movement, Space.World);

        // Прицел по мыши (отдельный Action "Aim")
        Vector2 mousePos = Mouse.current.position.ReadValue();
        Ray ray = Camera.main.ScreenPointToRay(mousePos);
        if (Physics.Raycast(ray, out RaycastHit hit))
        {
            transform.LookAt(new Vector3(hit.point.x, transform.position.y, hit.point.z));
        }
    }

    void TogglePause()
    {
        Time.timeScale = Time.timeScale > 0 ? 0 : 1;
        Debug.Log("Paused: " + (Time.timeScale == 0));
    }

    void Shoot()
    {
        // Instantiate + effects
        Debug.Log("Bang!");
    }
}
```
*Применение*: Move — геймпад/клавиши, Shoot — мышь/A.

#### **Пример 2: Ремаппинг в Runtime (Меню Настроек)**
```csharp
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.UI;

public class InputRemapper : MonoBehaviour
{
    public InputActionAsset actions;
    public Button rebindButton;
    private InputAction shootAction;

    void Start()
    {
        shootAction = actions.FindAction("Shoot");
        rebindButton.onClick.AddListener(StartRebind);
    }

    void StartRebind()
    {
        var rebind = shootAction.PerformInteractiveRebinding()
            .WithTargetBinding(0)  // Первый binding
            .WithCancelingKeyCode(KeyCode.Escape)
            .OnComplete(operation => 
            {
                rebindButton.GetComponentInChildren<Text>().text = "Rebound: " + shootAction.bindings[0].effectivePath;
                operation.Dispose();
            })
            .Start();

        // UI: "Press key..." пока rebind
    }
}
```
*Применение*: Кнопка в меню — игрок меняет Shoot на любую клавишу.

#### **Пример 3: Device Switch (Клавиатура → Геймпад)**
```csharp
using UnityEngine.InputSystem;

public class DeviceSwitcher : MonoBehaviour
{
    private PlayerInput playerInput;

    void Start()
    {
        playerInput = GetComponent<PlayerInput>();
    }

    void Update()
    {
        // Авто-свитч на геймпад
        if (Gamepad.current != null && Gamepad.current.wasUpdatedThisFrame)
        {
            playerInput.SwitchCurrentActionMap("Gamepad");  // Отдельный Map
        }
        else if (Keyboard.current != null && Keyboard.current.wasUpdatedThisFrame)
        {
            playerInput.SwitchCurrentActionMap("Keyboard");
        }
    }
}
```
*Применение*: Подключи геймпад — ввод переключается автоматически.

## 💡 **Продвинутые Фишки New Input System**
- **Composite Bindings**: 2D Vector (WASD как composite).  
- **Interactions**: Hold (удержание для чарджа), Press and Release.  
- **Localization**: Bindings для разных языков/регионов.  

**Эксперимент**: Создай InputActions, настрой Move — протестируй с геймпадом (Device Simulator).

*Ссылка на продвинутые примеры*: [Input System Advanced](https://learn.unity.com/tutorial/input-system-advanced).

## Заключение: Ввод — Твой Нервный Центр! 🎮
New Input System — гибкий, современный: ремаппинг, события, мульти-device. В шутере это WASD + контроллер без боли. Освой — и игры для всех!  

**Что Далее?**  
- Курс окончен! Мигрируй старый код.  
- Вопросы: [Unity Learn: New Input System](https://learn.unity.com/pathway/input-system).  

Ты переключил ввод — теперь игра отзывчива! Продолжай, контроллер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*