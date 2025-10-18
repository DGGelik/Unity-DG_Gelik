# 📜 Основы C# и MonoBehaviour: Первые Заклинания Кода

Привет, кодер-новичок! 📜 Эта глава теории — твой полный гид по **основам C# и MonoBehaviour в Unity**: от базового синтаксиса (переменные, условия, циклы) до MonoBehaviour как "скелета" скриптов (Start, Update, Awake). Мы заложим фундамент программирования для твоего топ-даун шутера: простой скрипт движения, проверки условий (стрельба если ammo >0). C# — мощный, но дружелюбный язык; MonoBehaviour — мост к Unity. Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (из курса, с ареной).  
- Visual Studio (открывает скрипты автоматически).  

**Предупреждение**: C# чувствителен к регистру (int != Int) — пиши точно. MonoBehaviour требует using UnityEngine;. Если ошибки компиляции — читай Console (красные строки). Тестируй скрипты в Play Mode!  

Готов закодить первую строку? По основам, как по алфавиту! 📝

## 🎯 **Что Такое C# и MonoBehaviour и Зачем Они Нужны?**
**C#** (произносится "си шарп") — объектно-ориентированный язык от Microsoft, основа скриптинга в Unity. Он простой для новичков, но мощный для игр (типы, классы, события).  

- **Для чего C#?**  
  - Логика: If (ammo >0) — стрельба.  
  - Данные: int score = 0; — очки.  
  - Функции: void Shoot() { /* bang */ } — перезапуск.  

**MonoBehaviour** — базовый класс для скриптов Unity: добавь к объекту — получи lifecycle методы (Awake, Start, Update). Без него скрипт — просто код, не взаимодействует с Unity.  

В твоём шутере: MonoBehaviour на герое — Update() для WASD, Start() для инициализации оружия. C# делает игру "умной", MonoBehaviour — "живой". Без них — статичные ассеты.  

**Как Начать?**: Create > C# Script — Unity генерирует MonoBehaviour.  

*Ссылка на официальную документацию*: [C# in Unity](https://docs.unity3d.com/Manual/ScriptingSection.html) и [MonoBehaviour](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html).

## 🔄 **Основы Синтаксиса C#: Переменные, Условия, Циклы**
C# — структурированный: блоки {}, точки с запятой ;.  

### **Переменные и Типы (Данные)**
Переменные — "коробки" для значений. Объяви: тип имя = значение;.

| Тип | Описание | Пример | В Шутере |
|-----|----------|--------|----------|
| **int** | Целое число. | int ammo = 30; | Очки: int score = 0; |
| **float** | Дробное. | float speed = 5.5f; | Скорость пули: float bulletSpeed = 20f; |
| **bool** | Да/Нет. | bool isShooting = false; | Стрельба: bool canShoot = ammo > 0; |
| **string** | Текст. | string playerName = "Hero"; | HUD: string ammoText = "Ammo: " + ammo; |
| **Vector3** | 3D-вектор (Unity). | Vector3 position = new Vector3(0,1,0); | Позиция героя: transform.position; |

**Код: Объявление и Изменение**:
```csharp
public class BasicVariables : MonoBehaviour
{
    int health = 100;  // Поле класса (видно в Inspector с [SerializeField])
    [SerializeField] private float damage = 20f;  // Приватное, но редактируемое

    void Start()
    {
        health -= damage;  // Вычитание
        Debug.Log("Health left: " + health);  // Вывод в Console
    }
}
```
*Применение*: Добавь к врагу — health снижается при уроне.

### **Условия (If/Else) — Решения**
If — "если": выполни код, если true.

**Код: Проверка для Стрельбы**:
```csharp
public class ShootingCondition : MonoBehaviour
{
    public int ammo = 30;

    void Update()
    {
        if (Input.GetMouseButtonDown(0) && ammo > 0)  // Если клик И ammo >0
        {
            Shoot();
            ammo--;  // Минус патрон
            Debug.Log("Bang! Ammo: " + ammo);
        }
        else if (ammo <= 0)
        {
            Debug.Log("Out of ammo! Reload.");
        }
    }

    void Shoot()
    {
        // Instantiate пули
    }
}
```
*Применение*: В PlayerMovement — стрельба только с ammo.

**Switch**: Для множественного выбора.
```csharp
switch (enemyType)
{
    case EnemyType.Basic: damage = 10; break;
    case EnemyType.Boss: damage = 50; break;
    default: damage = 5; break;
}
```

### **Циклы (Loops) — Повторы**
For/While — повторяй код.

**Код: Спавн Волны Врагов**:
```csharp
public class SpawnLoop : MonoBehaviour
{
    public GameObject enemyPrefab;
    public int waveSize = 5;

    void SpawnWave()
    {
        for (int i = 0; i < waveSize; i++)  // От 0 до waveSize-1
        {
            Vector3 pos = new Vector3(Random.Range(-10,10), 0, Random.Range(-10,10));
            Instantiate(enemyPrefab, pos, Quaternion.identity);
        }
    }

    // While: Пока ammo >0
    void ShootLoop()
    {
        while (ammo > 0)
        {
            Shoot();
            ammo -= 5;  // Бурст-стрельба
        }
    }
}
```
*Применение*: В EnemySpawner — for для волны.

*Ссылка на документацию*: [C# Basics](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/).

## 🛠️ **MonoBehaviour: Lifecycle и Методы**
MonoBehaviour — класс с методами, вызываемыми Unity автоматически.

| Метод | Когда Вызывается | Описание | Пример в Шутере |
|-------|------------------|----------|-----------------|
| **Awake()** | При создании (до Start). | Инициализация (ссылки). | rb = GetComponent<Rigidbody>(); |
| **Start()** | Первый кадр (после Awake). | Setup (UI, события). | UIHealthManager.UpdateUI(); |
| **Update()** | Каждый кадр (~60/сек). | Input, логика. | if (Input.GetKeyDown(KeyCode.Space)) Jump(); |
| **FixedUpdate()** | Фиксировано (для физики). | Движение, силы. | rb.AddForce(gravity); |
| **LateUpdate()** | После Update (для камеры). | Следование камере. | camera.transform.position = transform.position + offset; |
| **OnDestroy()** | При уничтожении. | Cleanup (сохрани данные). | PlayerPrefs.SetInt("Score", score); |

**Код: Базовый MonoBehaviour для Героя**:
```csharp
using UnityEngine;

public class HeroMonoBehaviour : MonoBehaviour
{
    private Rigidbody rb;
    public float jumpForce = 5f;

    void Awake()
    {
        // Ранний init — до других скриптов
        rb = GetComponent<Rigidbody>();
        Debug.Log("Hero Awake: " + gameObject.name);
    }

    void Start()
    {
        // После всех Awake
        rb.useGravity = true;
        Debug.Log("Hero Start: Ready to fight!");
    }

    void Update()
    {
        // Input каждый кадр
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Jump();
        }
    }

    void FixedUpdate()
    {
        // Физика
        Vector3 move = new Vector3(Input.GetAxis("Horizontal"), 0, Input.GetAxis("Vertical"));
        rb.velocity = new Vector3(move.x * speed, rb.velocity.y, move.z * speed);
    }

    void Jump()
    {
        rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
    }

    void OnDestroy()
    {
        Debug.Log("Hero Destroyed: Game Over!");
    }
}
```
*Применение*: Добавь к герою — lifecycle работает!

*Ссылка на документацию*: [MonoBehaviour Lifecycle](https://docs.unity3d.com/Manual/ExecutionOrder.html).

## 🎮 **Практические Примеры C# и MonoBehaviour в Твоём Шутере**
Готовый код — расширь PlayerMovement.

#### **Пример 1: Условия + Циклы для Волны (While + For)**
```csharp
public class WaveSystem : MonoBehaviour
{
    public int waveNumber = 1;
    public int enemiesPerWave = 3;

    void StartWave()
    {
        int spawned = 0;
        while (spawned < enemiesPerWave)  // Пока не все
        {
            for (int i = 0; i < waveNumber; i++)  // По сложности
            {
                if (spawned >= enemiesPerWave) break;
                SpawnEnemy();
                spawned++;
            }
            waveNumber++;  // Следующая сложнее
        }
    }

    void SpawnEnemy()
    {
        // Instantiate...
    }
}
```
*Применение*: Волны растут — for для спавна, while для контроля.

#### **Пример 2: MonoBehaviour для Менеджера Очков (Start + Update)**
```csharp
public class ScoreManager : MonoBehaviour
{
    public static ScoreManager Instance;
    public int score = 0;
    public Text scoreText;  // UI

    void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);  // Между сценами
        }
        else Destroy(gameObject);
    }

    void Start()
    {
        score = PlayerPrefs.GetInt("SavedScore", 0);  // Загрузка
        UpdateUI();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.R))  // Рестарт
        {
            ResetScore();
        }
    }

    public void AddScore(int points)
    {
        score += points;
        PlayerPrefs.SetInt("SavedScore", score);  // Сохранение
        UpdateUI();
    }

    void UpdateUI()
    {
        scoreText.text = "Score: " + score;
    }

    void ResetScore()
    {
        score = 0;
        UpdateUI();
    }
}
```
*Применение*: Singleton — очки сохраняются, Update для рестарта.

#### **Пример 3: Цикл + Условие для Патронов (For + If)**
```csharp
public void BurstFire(int bursts = 3)
{
    for (int i = 0; i < bursts; i++)
    {
        if (ammo > 0)
        {
            Shoot();
            ammo--;
        }
        else
        {
            Debug.Log("Out of ammo mid-burst!");
            break;  // Выход из цикла
        }
        yield return new WaitForSeconds(0.1f);  // Задержка (Coroutine)
    }
}
```
*Применение*: Burst-режим — for для выстрелов, if для ammo.

## 💡 **Продвинутые Фишки C# и MonoBehaviour**
- **Coroutines**: yield return для асинхронности (задержки без блокировки).  
- **Events/Delegates**: События (OnScoreChanged += UpdateUI;).  
- **Generics**: List<T> для пулов (List<Bullet>).  

**Эксперимент**: Создай скрипт с if (Input.Space) Jump() — протестируй в Update.

*Ссылка на продвинутые примеры*: [C# Advanced](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/).

## Заключение: C# и MonoBehaviour — Твой Кодовый Арсенал 📜
C# — логика (if, loops), MonoBehaviour — Unity-интеграция (Update для ввода). В шутере это движение + стрельба. Освой — и пиши хиты!  

**Что Далее?**  
- Перейди к [Input, События, Корутины](./Events.md) — углубим.  
- Вопросы: [Unity Learn: C# Basics](https://learn.unity.com/tutorial/c-sharp-basics).  

Ты закодил основы — теперь программируй миры! Продолжай, скриптер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*