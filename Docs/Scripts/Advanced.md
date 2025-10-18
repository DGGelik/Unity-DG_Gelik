# 📦 Продвинутые Техники: ScriptableObjects — Данные Без Объектов

Привет, дата-архитектор! 📦 Эта глава продвинутой теории — твой полный гид по **ScriptableObjects (SO)** в Unity: как создавать "банки данных" вне GameObject (конфиги врагов, оружия, уровней), чтобы код был чистым и переиспользуемым. SO — это классы для статичных данных: редактируй в Inspector, не дублируй код. Мы применим в твоём топ-даун шутере: SO для типов врагов (health, speed), оружия (damage, rate) — легко балансировать! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 5+).  
- Папка ScriptableObjects в Assets.  

**Предупреждение**: SO — не MonoBehaviour, нет Update/Start (статичные). Не Instantiate SO — они singleton-like. Если ссылки "розовеют" — проверь путь в Project. SO экономят память, но не для динамики (используй классы). Тестируй изменения в Play — SO обновляются на лету.  

Готов вынести данные из кода? По шагам, как по базам! 📝

## 🎯 **Что Такое ScriptableObjects и Зачем Они Нужны?**
**ScriptableObject** — класс для хранения данных как ассетов (файлы в Project), не привязанных к объектам. Это "конфиги": создай SO "EnemyStats", редактируй health/speed в Inspector — все враги используют.  

- **Для чего SO?**  
  - Баланс: Измени damage в SO — все пули обновятся.  
  - Переиспользование: Один SO для всех типов оружия.  
  - Чистый код: Данные отдельно от логики (не в скриптах).  

**Преимущества vs Классы**:  
- SO — ассеты: Сериализуются (сохраняются), видны в Inspector.  
- Классы — в памяти: Для runtime (временные данные).  

В твоём шутере: SO WeaponConfig (ammo, damage) — легко добавить пистолет/лазер без переписывания кода. Без SO — магические числа в скриптах (hardcoded).  

**Как Создать?**: [CreateAssetMenu] в классе, правой кнопкой > Create > [Имя].  

*Ссылка на официальную документацию*: [ScriptableObject Overview](https://docs.unity3d.com/Manual/class-ScriptableObject.html).

## 🔄 **Как Работают ScriptableObjects? (Создание, Сериализация, Использование)**
SO наследуют ScriptableObject: создай файл-ассет, редактируй поля.  

- **Сериализация**: [SerializeField] для приватных, [Header] для групп.  
- **Использование**: Public SO field в скрипте — перетащи в Inspector.  
- **События**: SO не имеет lifecycle, но можешь добавить OnValidate() для авто-теста.  

**Распространённые Ошибки**:  
- Забыл [CreateAssetMenu] — нет в меню.  
- Cyclic Reference: SO ссылается на себя — краш.  
- Не [System.Serializable] для вложенных классов.  

**Пример в Шутере: Создание SO для Врага**:
```csharp
using UnityEngine;

[CreateAssetMenu(fileName = "EnemyConfig", menuName = "Configs/EnemyConfig")]
public class EnemyConfig : ScriptableObject
{
    [Header("Basic Stats")]
    public string enemyName = "Basic Drone";
    public int maxHealth = 50;
    public float speed = 2f;
    public int scoreValue = 10;

    [Header("Advanced")]
    public Color enemyColor = Color.red;
    public AudioClip deathSound;

    [System.Serializable]
    public class DropItem
    {
        public GameObject itemPrefab;
        public float dropChance = 0.3f;
    }

    public DropItem[] possibleDrops;

    // OnValidate: Авто-проверка
    void OnValidate()
    {
        if (speed < 0) speed = 0;  // Не отрицательная скорость
        Debug.Log($"Validated {enemyName}: Health {maxHealth}");
    }
}
```
*Применение*: Создай файл (правой > Create > Configs > EnemyConfig) — редактируй в Inspector.

*Ссылка на документацию*: [CreateAssetMenu](https://docs.unity3d.com/ScriptReference/CreateAssetMenuAttribute.html).

## ⚙️ **Свойства и Методы ScriptableObjects**
SO — как класс, но ассет: поля сериализуются, методы вызываются вручную.

| Атрибут | Описание | Пример в Шутере | Код |
|---------|----------|-----------------|-----|
| **[CreateAssetMenu]** | Меню создания. | menuName = "Enemy/Drone". | [CreateAssetMenu(menuName = "Enemy/Drone")] |
| **[Header("Group")]** | Группа в Inspector. | "Stats" для health/speed. | [Header("Stats")] public int health; |
| **[SerializeField]** | Поле в Inspector (даже private). | private float damage — редактируй. | [SerializeField] private float damage; |
| **OnValidate()** | При изменении в Inspector. | Если health<0 — сброс. | void OnValidate() { if (health < 1) health = 1; } |
| **[System.Serializable] class Inner** | Вложенный класс. | DropItem с prefab/chance. | [System.Serializable] public class DropItem { ... } |

**Код: Использование SO в Скрипте (EnemyAI)**:
```csharp
using UnityEngine;

public class EnemyUsingSO : MonoBehaviour
{
    public EnemyConfig config;  // Перетащи SO в Inspector

    private float currentHealth;

    void Start()
    {
        currentHealth = config.maxHealth;
        GetComponent<Renderer>().material.color = config.enemyColor;  // Цвет из SO
        Debug.Log($"Spawned {config.enemyName} with {config.speed} speed");
    }

    public void TakeDamage(int dmg)
    {
        currentHealth -= dmg;
        if (currentHealth <= 0)
        {
            Die();
        }
    }

    void Die()
    {
        // Дроп из SO
        foreach (var drop in config.possibleDrops)
        {
            if (Random.value < drop.dropChance)
            {
                Instantiate(drop.itemPrefab, transform.position, Quaternion.identity);
            }
        }

        // Звук
        if (config.deathSound != null)
        {
            AudioSource.PlayClipAtPoint(config.deathSound, transform.position);
        }

        GameManager.Instance.AddScore(config.scoreValue);  // Очки
        Destroy(gameObject);
    }
}
```
*Применение*: Спавнь врага — используй config.speed для движения.

*Ссылка на документацию*: [OnValidate](https://docs.unity3d.com/ScriptReference/ScriptableObject.OnValidate.html).

## 💻 **Практические Примеры ScriptableObjects в Твоём Шутере**
Готовый код — балансируй без перекомпиляции.

#### **Пример 1: SO для Оружия (Damage, Rate)**
```csharp
[CreateAssetMenu(fileName = "WeaponConfig", menuName = "Weapons/WeaponConfig")]
public class WeaponConfig : ScriptableObject
{
    public string weaponName = "Pistol";
    public int damage = 20;
    public float fireRate = 0.5f;  // Секунд между выстрелами
    public int ammoCapacity = 30;
    public AudioClip fireSound;
    public GameObject bulletPrefab;

    [System.Serializable]
    public class Upgrade
    {
        public string upgradeName;
        public float multiplier = 1.5f;  // Урон *1.5
    }

    public Upgrade[] upgrades;
}

public class WeaponSystem : MonoBehaviour
{
    public WeaponConfig currentWeapon;

    private float lastFireTime;

    void Update()
    {
        if (Input.GetMouseButtonDown(0) && Time.time > lastFireTime + currentWeapon.fireRate)
        {
            Fire();
            lastFireTime = Time.time;
        }
    }

    void Fire()
    {
        Instantiate(currentWeapon.bulletPrefab, firePoint.position, firePoint.rotation);
        AudioSource.PlayClipAtPoint(currentWeapon.fireSound, transform.position);
        // Урон из SO
        // raycastHit.collider.GetComponent<EnemyHealth>().TakeDamage(currentWeapon.damage);
    }

    public void UpgradeWeapon(int upgradeIndex)
    {
        if (upgradeIndex < currentWeapon.upgrades.Length)
        {
            currentWeapon.damage *= currentWeapon.upgrades[upgradeIndex].multiplier;
            Debug.Log($"Upgraded {currentWeapon.weaponName}!");
        }
    }
}
```
*Применение*: Создай SO "LaserGun" — damage=50, перетащи в WeaponSystem.

#### **Пример 2: SO для Уровня (Волны, Бонусы)**
```csharp
[CreateAssetMenu(fileName = "LevelConfig", menuName = "Levels/LevelConfig")]
public class LevelConfig : ScriptableObject
{
    public string levelName = "Arena 1";
    public int[] enemiesPerWave = { 3, 5, 8 };  // Волны
    public float waveDelay = 2f;
    public GameObject[] bonusPrefabs;
    public float bonusSpawnChance = 0.2f;
}

public class LevelManager : MonoBehaviour
{
    public LevelConfig currentLevel;

    private int currentWave = 0;

    void StartWave()
    {
        StartCoroutine(WaveCoroutine());
    }

    IEnumerator WaveCoroutine()
    {
        for (int i = 0; i < currentLevel.enemiesPerWave[currentWave]; i++)
        {
            SpawnEnemy();
            yield return new WaitForSeconds(currentLevel.waveDelay);
        }

        // Бонус после волны
        if (Random.value < currentLevel.bonusSpawnChance)
        {
            int randomBonus = Random.Range(0, currentLevel.bonusPrefabs.Length);
            Instantiate(currentLevel.bonusPrefabs[randomBonus], RandomPos(), Quaternion.identity);
        }

        currentWave++;
        if (currentWave < currentLevel.enemiesPerWave.Length)
        {
            StartCoroutine(WaveCoroutine());  // Следующая
        }
    }
}
```
*Применение*: SO "BossArena" — enemiesPerWave={1} (босс), перетащи в менеджер.

#### **Пример 3: Массовый Баланс (SO + Editor Script)**
```csharp
// Editor скрипт (в папке Editor)
#if UNITY_EDITOR
using UnityEngine;
using UnityEditor;

[CustomEditor(typeof(WeaponConfig))]
public class WeaponConfigEditor : Editor
{
    public override void OnInspectorGUI()
    {
        DrawDefaultInspector();  // Стандарт

        WeaponConfig config = (WeaponConfig)target;
        if (GUILayout.Button("Balance Test"))
        {
            config.damage = Mathf.RoundToInt(config.damage * 1.1f);  // +10% урон
            config.fireRate *= 0.9f;  // Быстрее
            EditorUtility.SetDirty(config);  // Сохрани
        }
    }
}
#endif
```
*Применение*: Кнопка в Inspector SO — автобаланс для тестирования.

## 💡 **Продвинутые Фишки ScriptableObjects**
- **SO как Database**: List<SO> для коллекций (все оружия).  
- **Events в SO**: [Serializable] UnityEvent для реакций.  
- **Addressables**: Загружай SO асинхронно для больших проектов.  

**Эксперимент**: Создай SO EnemyConfig, перетащи в EnemyAI — измени speed, спавнь — эффект мгновенный!

*Ссылка на продвинутые примеры*: [ScriptableObjects Advanced](https://learn.unity.com/tutorial/scriptable-objects-advanced).

## Заключение: SO — Ключ к Чистому Коду 📦
ScriptableObjects — "банки" данных: балансируй без кода, переиспользуй. В шутере это оружие/враги в файлах — легко модить. Освой — и проекты масштабируемы!  

**Что Далее?**  
- Перейди к [Дополнительно: Шейдеры и Материалы](./ShadersMaterials.md) — визуалы.  
- Вопросы: [Unity Learn: ScriptableObjects](https://learn.unity.com/tutorial/scriptable-objects).  

Ты вынес данные — код чист! Продолжай, конфигер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*