# ⚡ Input, События, Корутины: Углубляемся в Динамику Кода

Привет, динамист событий! ⚡ Эта глава теории — твой полный гид по **Input (ввод), Events (событиям) и Coroutines (корутинам) в C#/Unity**: от обработки клавиш/мыши (WASD, клики) до событий (OnShoot для стрельбы) и асинхронных корутин (задержки без блокировки, как таймер перезарядки). Мы углубим код твоего топ-даун шутера: плавная стрельба с событиями, ввод для меню, корутины для волн врагов. Это шаг от базового к продвинутому — код становится отзывчивым! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 3+).  
- Скрипт PlayerMovement (для расширения).  

**Предупреждение**: Input.GetKeyDown — на кадр, GetKey — пока нажато (для hold). События — избегай memory leaks (отписывайся). Корутины останавливай StopCoroutine при смене сцены. Тестируй в Standalone (Game View не всегда точно имитирует). Если события не срабатывают — проверь UnityEvents в Inspector.  

Готов к отзывчивости? По темам, как по триггерам! 📝

## 🎯 **Что Такое Input, Events и Coroutines и Зачем Углубляться?**
**Input** — обработка ввода (клавиши, мышь, геймпад). **Events** — сигналы (OnCollision — событие касания). **Coroutines** — "подпрограммы" с yield (пауза без блокировки Update).  

- **Для чего Input?**  
  - Управление: WASD движение, мышь — прицел/стрельба.  
  - Новое: Геймпад (Input System package).  

- **Для чего Events?**  
  - Фидбек: OnShoot — звук + вспышка.  
  - Decoupling: Скрипты не знают друг друга (UnityEvent для UI).  

- **Для чего Coroutines?**  
  - Асинхронность: Перезарядка 2 сек без заморозки игры.  
  - Последовательность: Spawn → Wait → Уничтожь.  

В твоём шутере: Input для стрельбы, Event OnKillEnemy + очки, Coroutine для волны (spawn every 1 sec). Без них код — последовательный, лагающий.  

**Как Начать?**: Input.GetAxis("Horizontal"), UnityEvent в Inspector, StartCoroutine(MyRoutine()).  

*Ссылка на официальную документацию*: [Input System](https://docs.unity3d.com/Manual/Input.html), [Events](https://docs.unity3d.com/Manual/UnityEvents.html), [Coroutines](https://docs.unity3d.com/Manual/Coroutines.html).

## 🔄 **Как Работает Input? (Старый vs Новый System)**
Input — мост от устройства к коду: старый (Input Manager) простой, новый (Input System) гибкий.  

- **Старый Input**: GetKey/GetAxis — в Update().  
- **Новый**: Input Actions — asset для геймпада/тача, меньше кода.  

**Распространённые Ошибки**:  
- GetKeyDown vs GetKey: Down — один кадр, Key — hold.  
- Axis не настроены: Edit > Project Settings > Input Manager.  
- Мобильный: Touch вместо Mouse — используй новый System.  

**Свойства Input**:
| Метод | Описание | Пример в Шутере | Альтернатива |
|-------|----------|-----------------|--------------|
| **GetKeyDown(KeyCode key)** | Нажатие (один кадр). | if (GetKeyDown(KeyCode.Mouse0)) Shoot(); | Новый: action.triggered |
| **GetKey(KeyCode key)** | Держится. | if (GetKey(KeyCode.W)) MoveForward(); | action.ReadValue<float>() |
| **GetAxis(string axis)** | Аналог ( -1 to 1). | float h = GetAxis("Horizontal"); | Новый: Vector2 move = action.ReadValue<Vector2>(); |
| **GetMouseButtonDown(int button)** | Клик мыши. | if (GetMouseButtonDown(0)) Aim(); | Новый: pointer.down |

**Код: Базовый Input для Движения + Стрельбы** (расширь PlayerMovement):
```csharp
using UnityEngine;

public class AdvancedInput : MonoBehaviour
{
    public float moveSpeed = 5f;
    public Camera mainCamera;

    void Update()
    {
        // Input для движения
        float horizontal = Input.GetAxisRaw("Horizontal");  // -1/0/1, без сглаживания
        float vertical = Input.GetAxisRaw("Vertical");
        Vector3 moveDir = new Vector3(horizontal, 0, vertical).normalized * moveSpeed * Time.deltaTime;
        transform.Translate(moveDir, Space.World);

        // Input для стрельбы (hold для авто)
        if (Input.GetMouseButton(0))  // Держится
        {
            ShootContinuous();
        }
        else if (Input.GetMouseButtonDown(0))  // Один клик
        {
            ShootSingle();
        }

        // Прицел: Мышь
        Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
        if (Physics.Raycast(ray, out RaycastHit hit))
        {
            transform.LookAt(hit.point);  // Поворот к курсору
        }
    }

    void ShootContinuous()
    {
        // Авто-стрельба
        Debug.Log("Auto-fire!");
    }

    void ShootSingle()
    {
        // Одиночный
        Debug.Log("Bang!");
    }
}
```
*Применение*: WASD + мышь — полный контроль, Raycast для прицела.

*Ссылка на документацию*: [Input.GetAxis](https://docs.unity3d.com/ScriptReference/Input.GetAxis.html).

## 🔔 **Как Работают События? (UnityEvents и Delegates)**
События — "сигналы": один скрипт уведомляет другие (OnDamage — обнови UI).  

- **UnityEvent**: В Inspector — визуально, для UI/анимаций.  
- **Delegates/Events**: C# — Action/Func для кода.  

**Распространённые Ошибки**:  
- Не отписывайся: += event; — leaks (используй -=).  
- Null — проверь listener.  

**Свойства Events**:
| Тип | Описание | Пример в Шутере | Код |
|-----|----------|-----------------|-----|
| **UnityEvent** | Простой (no params). | OnEnemyKilled.Invoke(); | [SerializeField] UnityEvent onKill; |
| **UnityEvent<int>** | С параметром. | onScore.Invoke(points); | UnityEvent<int> onScore; |
| **C# Event** | Delegate. | event Action<int> OnDamage; | public event Action<int> OnDamage; |

**Код: Система Событий для Урона + Очков** (расширь EnemyHealth):
```csharp
using UnityEngine;
using UnityEngine.Events;
using System;

public class EventSystemExample : MonoBehaviour
{
    [SerializeField] UnityEvent onDeath;  // Inspector: Добавь UI.UpdateScore
    public event Action<int> onDamageTaken;  // C# событие
    public int health = 100;

    public void TakeDamage(int dmg)
    {
        health -= dmg;
        onDamageTaken?.Invoke(dmg);  // ? — safe call

        if (health <= 0)
        {
            onDeath.Invoke();  // UnityEvent
            // Подписка: void Start() { onDamageTaken += HandleDamage; }
        }
    }

    void HandleDamage(int dmg)
    {
        Debug.Log("Ouch! " + dmg + " damage!");
    }

    void OnDestroy()
    {
        onDamageTaken -= HandleDamage;  // Отписка — no leaks
    }
}
```
*Применение*: OnDeath — +очки, onDamage — мигание UI.

*Ссылка на документацию*: [UnityEvents](https://docs.unity3d.com/Manual/UnityEvents.html).

## ⏳ **Как Работают Корутины? (Асинхронный Код с Yield)**
Корутины — "подпрограммы" с паузами: yield return new WaitForSeconds(1f) — жди 1 сек без остановки Update.  

- **StartCoroutine**: Запуск.  
- **yield**: Типы — WaitForSeconds, WaitForEndOfFrame, null (следующий кадр).  

**Распространённые Ошибки**:  
- Не StopCoroutine — зомби-рутины (StopAllCoroutines в OnDestroy).  
- В Update — не для корутин, используй FixedUpdate для физики.  

**Свойства Корутин**:
| Yield | Описание | Пример в Шутере | Код |
|-------|----------|-----------------|-----|
| **WaitForSeconds(float)** | Пауза. | Перезарядка 2 сек. | yield return new WaitForSeconds(2f); |
| **WaitForEndOfFrame** | Конец кадра. | После рендера. | yield return new WaitForEndOfFrame(); |
| **WaitUntil(Func<bool>)** | Пока условие false. | Пока ammo=0. | yield return new WaitUntil(() => ammo > 0); |

**Код: Корутина для Волны + Перезарядки** (расширь EnemySpawner):
```csharp
using UnityEngine;
using System.Collections;

public class CoroutineExample : MonoBehaviour
{
    public GameObject enemyPrefab;
    private bool isReloading = false;

    void Start()
    {
        StartCoroutine(SpawnWaveCoroutine());  // Запуск
    }

    IEnumerator SpawnWaveCoroutine()
    {
        for (int i = 0; i < 5; i++)
        {
            Instantiate(enemyPrefab, RandomPos(), Quaternion.identity);
            yield return new WaitForSeconds(1f);  // Пауза 1 сек
        }

        // Цепочка: Жди конец волны
        yield return new WaitUntil(() => NoEnemiesAlive());  // Функция: bool NoEnemiesAlive() { return FindObjectsOfType<Enemy>().Length == 0; }

        StartCoroutine(ReloadCoroutine());  // Следующая
    }

    IEnumerator ReloadCoroutine()
    {
        isReloading = true;
        Debug.Log("Reloading...");

        float reloadTime = 3f;
        while (reloadTime > 0)
        {
            yield return new WaitForSeconds(0.1f);  // Тик каждый 0.1 сек
            reloadTime -= 0.1f;
            // UI: ammoText.text = "Reloading: " + Mathf.Round(reloadTime);
        }

        isReloading = false;
        Debug.Log("Reloaded!");
    }

    Vector3 RandomPos()
    {
        return new Vector3(Random.Range(-10,10), 0, Random.Range(-10,10));
    }

    bool NoEnemiesAlive()
    {
        return FindObjectsOfType<EnemyAI>().Length == 0;
    }

    void OnDestroy()
    {
        StopAllCoroutines();  // Остановка при смене сцены
    }
}
```
*Применение*: Волны спавнятся с паузами, перезарядка — без лагов.

*Ссылка на документацию*: [StartCoroutine](https://docs.unity3d.com/ScriptReference/MonoBehaviour.StartCoroutine.html).

## 🎮 **Практические Примеры в Твоём Шутере**
Готовый код — углуби PlayerMovement.

#### **Пример 1: Input + Event для Стрельбы (Мышь + OnShoot)**
```csharp
using UnityEngine;
using UnityEngine.Events;

public class InputEventExample : MonoBehaviour
{
    [SerializeField] UnityEvent onShootEvent;  // Inspector: Добавь Audio.Play
    private float lastShootTime;
    public float fireRate = 0.2f;

    void Update()
    {
        // Input: Hold fire
        if (Input.GetMouseButton(0) && Time.time > lastShootTime + fireRate)
        {
            Shoot();
            lastShootTime = Time.time;
        }

        // Альтернатива: Геймпад
        if (Input.GetButtonDown("Fire1"))  // Input Manager: Fire1 = Space/Mouse0
        {
            Shoot();
        }
    }

    void Shoot()
    {
        // Raycast для прицела
        Ray ray = Camera.main.ScreenPointToRay(Input.mousePosition);
        if (Physics.Raycast(ray, out RaycastHit hit))
        {
            // Пуля к hit.point
            Instantiate(bulletPrefab, transform.position, Quaternion.LookRotation(hit.point - transform.position));
        }

        onShootEvent.Invoke();  // Событие: Звук, свет, анимация
    }
}
```
*Применение*: Событие OnShoot — мигание UI + звук.

#### **Пример 2: Coroutine + Event для Перезарядки**
```csharp
using System.Collections;

public class ReloadCoroutine : MonoBehaviour
{
    public UnityEvent onReloadComplete;  // UI: "Ready!"
    public float reloadTime = 2f;
    private bool reloading = false;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.R) && !reloading)
        {
            StartCoroutine(Reload());
        }
    }

    IEnumerator Reload()
    {
        reloading = true;
        onReloadComplete.Invoke();  // UI: "Reloading..." (параметр? Используй overload)

        float elapsed = 0f;
        while (elapsed < reloadTime)
        {
            elapsed += Time.deltaTime;
            float progress = elapsed / reloadTime;
            // UI Slider: fillAmount = progress
            yield return null;  // Следующий кадр
        }

        ammo = maxAmmo;
        reloading = false;
        onReloadComplete.Invoke();  // "Reloaded!"
    }
}
```
*Применение*: Прогресс-бар перезарядки — yield без блоков.

#### **Пример 3: События для Глобальной Логики (OnKillEnemy)**
```csharp
using System;

public class GlobalEvents : MonoBehaviour
{
    public static event Action<int> OnEnemyKilled;  // Params: очки
    public static event Action OnGameOver;

    public void KillEnemy(int points)
    {
        OnEnemyKilled?.Invoke(points);  // Safe call
    }

    // Подписка в другом скрипте
    void OnEnable() => OnEnemyKilled += AddScore;
    void OnDisable() => OnEnemyKilled -= AddScore;

    void AddScore(int pts) => score += pts;
}
```
*Применение*: В EnemyHealth Die() — KillEnemy(10), UI реагирует.

## 💡 **Продвинутые Фишки Input, Events, Coroutines**
- **Новый Input System**: Asset > Input System — Actions asset для ремаппинга.  
- **Persistent Events**: ScriptableObject для глобальных (OnPause).  
- **Coroutine Pools**: Переиспользуй рутины для пулинга.  

**Эксперимент**: Добавь Coroutine WaitForSeconds(1f) в Update() — тест задержки.

*Ссылка на продвинутые примеры*: [Input System Deep Dive](https://learn.unity.com/tutorial/input-system-deep-dive).

## Заключение: Динамика — Сердце Кода! ⚡
Input — реакция, Events — связь, Coroutines — время. В шутере это стрельба без лагов, события для фидбека. Освой — и код "танцует"!  

**Что Далее?**  
- Перейди к [Продвинутые Техники](./Advanced.md) — ScriptableObjects.  
- Вопросы: [Unity Learn: Input and Events](https://learn.unity.com/tutorial/input-events-coroutines).  

Ты углубил динамику — теперь код пульсирует! Продолжай, триггер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*