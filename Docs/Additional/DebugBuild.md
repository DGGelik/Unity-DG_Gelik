# 🔨 Отладка и Сборка: Финальный Тест

Привет, отладчик-мастер! 🔨 Эта глава дополнительной теории — твой полный гид по **отладке и сборке в Unity**: от поиска багов (Console, Profiler, breakpoints) до финальной сборки (.exe с оптимизацией). Мы протестируем твой топ-даун шутер: исправим типичные ошибки (пули не попадают, UI не обновляется), оптимизируем FPS и соберём релиз. Это кульминация — игра готова к миру! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 10).  
- Visual Studio (для breakpoints).  

**Предупреждение**: Отладка — итеративно: фикс один баг, тест, следующий. Сборка на слабом ПК может зависнуть — закрой другие программы. Для релиза — убери Debug.Log, они засоряют билд. Тестируй на другом устройстве для реальных багов.  

Готов к последнему рубежу? По этапам, как по чек-листу! 📝

## 🎯 **Что Такое Отладка и Сборка и Зачем Финальный Тест?**
Отладка — "охота на баги": инструменты для поиска ошибок (краши, лаги). Сборка — упаковка в .exe/WebGL (File > Build). Финальный тест — симуляция релиза: баланс, производительность, краш-тесты.  

- **Для чего?**  
  - Отладка: Найди, почему пули проходят сквозь врагов (нет Collider?).  
  - Сборка: Готовая игра для друзей (не редактор).  
  - Тест: 30 мин игры — баланс ок? FPS >60?  

В твоём шутере: Отладь спавн (Profiler покажет лаги), собери .exe, тест на другом ПК — баги вылезут. Без этого — сырой прототип.  

**Как Начать?**: Window > General > Console для отладки, Build Settings для сборки.  

*Ссылка на официальную документацию*: [Debugging Overview](https://docs.unity3d.com/Manual/Debugging.html) и [Building Overview](https://docs.unity3d.com/Manual/PublishingBuilds.html).

## 🔄 **Как Работает Отладка? (Console, Profiler, Breakpoints)**
Отладка — системно: логгируй, профилируй, паузируй код.  

- **Console**: Логи ошибок (красные), предупреждений (жёлтые).  
- **Profiler**: CPU/GPU использование (Window > Analysis > Profiler).  
- **Breakpoints**: Пауза в VS (F9 на строке).  

**Распространённые Ошибки в Шутере**:  
- NullReference: Ссылка на prefab null (переприкрепи).  
- Infinite Loop: Спавн без лимита — FPS=0.  
- Memory Leak: Не Destroy объекты — RAM растёт.  

**Пример в Шутере: Отладка Стрельбы**:
```csharp
public class DebugShooting : MonoBehaviour
{
    public GameObject bulletPrefab;

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            if (bulletPrefab == null)
            {
                Debug.LogError("Bullet prefab is null! Check Inspector.");  // Красный лог
                return;
            }

            Debug.Log("Shooting! Position: " + transform.position);  // Жёлтый/белый

            GameObject bullet = Instantiate(bulletPrefab, transform.position, transform.rotation);
            // Profiler: Window > Analysis > Profiler — увидишь нагрузку на Instantiate
        }
    }
}
```
*Применение*: Добавь в PlayerMovement — Console покажет, если prefab сломан.

*Ссылка на документацию*: [Console and Log](https://docs.unity3d.com/Manual/DebuggingConsole.html).

## ⚙️ **Инструменты Отладки: Пошаговый Процесс**
Вот ключевые — используй в комбо.

| Инструмент | Описание | Ключевые Функции | Пример в Шутере |
|------------|----------|------------------|-----------------|
| **Console** | Логи и ошибки. | Debug.Log("Msg"), LogError/Warning, Clear (кнопка). | "Enemy spawned at " + position — трек спавна. |
| **Profiler** | Производительность. | CPU Usage, Memory, GPU — Hierarchy для bottleneck. | Instantiate пули — если >10% CPU, оптимизируй пул. |
| **Debugger (VS)** | Пауза кода. | Breakpoints (F9), Step Over (F10), Watch (переменные). | Пауза в OnTriggerEnter — проверь other.tag. |
| **Frame Debugger** | Рендер. | Window > Analysis > Frame Debugger — шаг по кадрам. | Свет от взрыва — видит ли GPU? |
| **Memory Profiler** | Память. | Window > Analysis > Memory Profiler — утечки. | Не Destroy враги — память растёт. |

**Код для Профилинга (Добавь в GameManager)**:
```csharp
using UnityEngine;
using UnityEngine.Profiling;  // Для Sampler

public class ProfilerHelper : MonoBehaviour
{
    void Update()
    {
        if (Input.GetKeyDown(KeyCode.P))
        {
            Profiler.BeginSample("EnemySpawnTest");
            SpawnEnemy();  // Твой метод
            Profiler.EndSample();
        }
    }

    void SpawnEnemy()
    {
        Instantiate(enemyPrefab, Random.insideUnitSphere * 10, Quaternion.identity);
    }
}
```
*Применение*: Нажми P в Play — Profiler покажет время на спавн.

*Ссылка на документацию*: [Profiler](https://docs.unity3d.com/Manual/Profiler.html).

## 🛠️ **Финальная Сборка: Оптимизация и Релиз**
Сборка — "упаковка" в .exe/APK.  

- **Build Settings**: Выбери платформу (PC Standalone), добавь сцены.  
- **Оптимизация**: Убери Unused Assets (Assets > Reimport All), сжимай текстуры (Import Settings > Compression).  
- **Player Settings**: Splash Screen off, Scripting Backend IL2CPP для скорости.  

**Распространённые Ошибки**:  
- Missing Scripts: Ссылки сломаны — переприкрепи.  
- Build Fail: Проверь Console на ошибки компиляции.  

**Пример Оптимизации в Шутере**:
```csharp
using UnityEngine;

public class BuildOptimizer : MonoBehaviour
{
    // Пул объектов вместо Instantiate (экономит память)
    public GameObject bulletPrefab;
    private Queue<GameObject> bulletPool = new Queue<GameObject>();
    public int poolSize = 20;

    void Start()
    {
        for (int i = 0; i < poolSize; i++)
        {
            GameObject bullet = Instantiate(bulletPrefab);
            bullet.SetActive(false);
            bulletPool.Enqueue(bullet);
        }
    }

    public GameObject GetBullet()
    {
        if (bulletPool.Count > 0)
        {
            GameObject bullet = bulletPool.Dequeue();
            bullet.SetActive(true);
            return bullet;
        }
        return Instantiate(bulletPrefab);  // Fallback
    }

    public void ReturnBullet(GameObject bullet)
    {
        bullet.SetActive(false);
        bulletPool.Enqueue(bullet);
    }
}
```
*Применение*: В стрельбе GetBullet() вместо Instantiate — FPS +20%.

*Ссылка на документацию*: [Build Optimization](https://docs.unity3d.com/Manual/OptimizingGraphicsPerformance.html).

## 🎮 **Практические Примеры Отладки и Сборки в Твоём Шутере**
Готовый код — рефакторинг.

#### **Пример 1: Отладка Урона (Логи + Breakpoints)**
```csharp
public class DamageDebugger : MonoBehaviour
{
    public int health = 100;

    public void TakeDamage(int dmg)
    {
        Debug.Log($"Taking {dmg} damage. Health before: {health}");  // Лог

        health -= dmg;

#if UNITY_EDITOR  // Только в редакторе
        if (health <= 0)
        {
            Debug.Break();  // Пауза для breakpoints
        }
#endif

        Debug.Log($"Health after: {health}");
    }
}
```
*Применение*: Добавь в EnemyHealth — Console покажет поток урона.

#### **Пример 2: Профилинг Спавна (Снижение Лагов)**
```csharp
using UnityEngine.Profiling;

public class SpawnProfiler : MonoBehaviour
{
    public void SpawnWave()
    {
        Profiler.BeginSample("Wave Spawn");
        for (int i = 0; i < 5; i++)
        {
            Profiler.BeginSample("Single Spawn");
            Instantiate(enemyPrefab, RandomSpawnPoint(), Quaternion.identity);
            Profiler.EndSample();
        }
        Profiler.EndSample();
    }

    Vector3 RandomSpawnPoint()
    {
        return new Vector3(Random.Range(-10, 10), 1, Random.Range(-10, 10));
    }
}
```
*Применение*: В EnemySpawner — Profiler покажет, если Instantiate лагит.

#### **Пример 3: Финальная Сборка с Оптимизацией (ScriptableObject для Настроек)**
```csharp
[CreateAssetMenu(fileName = "BuildSettings", menuName = "Build/BuildSettings")]
public class BuildSettings : ScriptableObject
{
    public bool enableDebugLogs = false;
    public int targetFPS = 60;
    public bool useIL2CPP = true;

    // В Build Settings скрипте
    public void ApplySettings()
    {
        Application.targetFrameRate = targetFPS;
        QualitySettings.vSyncCount = 0;  // Для FPS

        #if !UNITY_EDITOR
        if (!enableDebugLogs)
        {
            Debug.unityLogger.logEnabled = false;  // Выключи логи в билде
        }
        #endif
    }
}
```
*Применение*: Создай SO, вызови ApplySettings() в GameManager — билд лёгкий.

## 💡 **Продвинутые Фишки Отладки и Сборки**
- **Remote Debugging**: VS + Unity — breakpoints в билде.  
- **IL2CPP**: Для скорости, но дольше сборка.  
- **Addressables**: Асинхронная загрузка ассетов в билде.  

**Эксперимент**: Добавь Debug.Log в Update() — Console покажет FPS-дроп. Собери билд — логи исчезнут.

*Ссылка на продвинутые примеры*: [Advanced Debugging](https://learn.unity.com/tutorial/advanced-debugging).

## Заключение: Тест — Ключ к Релизу 🔨
Отладка находит баги, сборка упаковывает, тест подтверждает. В шутере это значит стабильный бой без крашей. Освой — и релизь шедевры!  

**Что Далее?**  
- Курс окончен! Поделись билдом на itch.io.  
- Вопросы: [Unity Learn: Debugging and Building](https://learn.unity.com/tutorial/debugging-and-building).  

Ты прошёл тест — игра готова к славе! Продолжай, релизер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*