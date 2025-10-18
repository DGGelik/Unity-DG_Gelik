# 🧱 Дополнительно: Сцены и Префабы — Организуем Код

Привет, архитектор кода! 🧱 Эта глава дополнительной теории — твой гид по **сценам и префабам в Unity**: как организовать проект (множество сцен для меню/уровней), префабы (шаблоны для повторного использования) и чистый код (организация скриптов, менеджеры). Мы упорядочим твой топ-даун шутер: префабы для пуль/врагов, сцены для меню/арены, чтобы код не был хаосом. Это фундамент масштаба — от прототипа к большой игре! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 10).  
- Папки в Project (Scenes, Prefabs, Scripts).  

**Предупреждение**: Префабы — ссылки, не копии: изменения в prefab обновляют все экземпляры. Если сцены не загружаются — проверь Build Settings. Организуй код заранее — потом переписывать больно!  

Готов упорядочить хаос? По разделам, как по полкам! 📝

## 🎯 **Что Такое Сцены и Префабы и Зачем Организовывать Код?**
Сцены — "картины" игры (меню, уровень), префабы — "готовые блоки" (враг как prefab — спавнь копии). Организация — чтобы код не был спагетти: папки, менеджеры, события.  

- **Для чего?**  
  - Сцены: Разделить игру (MainMenu.unity, Arena.unity) — загружай по клику.  
  - Префабы: Повторяемость — один prefab врага для всех волн.  
  - Организация: Скрипты в папках (Player/, Enemies/), singleton-менеджеры для глобального (GameManager).  

В твоём шутере: Сцена Menu загружает Arena, prefab Bullet для стрельбы, GameManager хранит очки между сценами. Без этого — дубликаты и баги.  

**Как Начать?**: File > New Scene для сцен, перетащи объект в Project для prefab.  

*Ссылка на официальную документацию*: [Scenes Overview](https://docs.unity3d.com/Manual/CreatingScenes.html) и [Prefabs Overview](https://docs.unity3d.com/Manual/Prefabs.html).

## 🔄 **Как Работают Сцены? (Загрузка, Переходы и Менеджеры)**
Сцены — отдельные файлы (.unity), загружаются SceneManager.  

- **Build Settings**: Добавь сцены в список (File > Build Settings > Add Open Scenes).  
- **Загрузка**: LoadScene("Arena") — синхронно, LoadSceneAsync — асинхронно с прогрессом.  
- **Переходы**: DontDestroyOnLoad(gameObject) — сохрани объект (очки) между сценами.  

**Распространённые Ошибки**:  
- Сцена не в Build — не загружается.  
- Lost References — префабы "розовеют" при смене сцены (переприкрепи).  
- Memory Leak — не уничтожай ненужные объекты.  

**Пример в Шутере: Переход Меню → Арена**:
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneTransition : MonoBehaviour
{
    public void LoadArena()
    {
        // Асинхронная загрузка с баром
        AsyncOperation asyncLoad = SceneManager.LoadSceneAsync("Arena");
        asyncLoad.allowSceneActivation = true;  // Авто-активация

        // Прогресс (прикрепи к UI Slider)
        StartCoroutine(LoadingBar(asyncLoad));
    }

    IEnumerator LoadingBar(AsyncOperation operation)
    {
        while (!operation.isDone)
        {
            float progress = Mathf.Clamp01(operation.progress / 0.9f);  // 0.9 = готовность
            // loadingSlider.value = progress;
            yield return null;
        }
    }
}
```
*Применение*: Клик на StartButton — плавный переход.

*Ссылка на документацию*: [SceneManager](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.html).

## 🏗️ **Как Работают Префабы? (Создание, Инстанс и Override)**
Префабы — "синие чертежи": создай один, спавнь копии, изменения обновят все.  

- **Создание**: Перетащи объект из Hierarchy в Project > Prefabs.  
- **Инстанс**: Instantiate(prefab, position, rotation).  
- **Override**: Изменения в экземпляре (Apply — в prefab, Revert — сброс).  

**Распространённые Ошибки**:  
- Nested Prefabs: Слишком глубокая иерархия — используй Nesting (Unity 2018+).  
- Broken Links: Переименуй prefab — ссылки сломаются.  

**Пример в Шутере: Спавн Префабов Врагов**:
```csharp
using UnityEngine;

public class PrefabSpawner : MonoBehaviour
{
    public GameObject enemyPrefab;  // Перетащи prefab в Inspector
    public Transform[] spawnPoints;

    void SpawnEnemy(int index)
    {
        // Инстанс с вариацией
        GameObject newEnemy = Instantiate(enemyPrefab, spawnPoints[index].position, Quaternion.identity);
        newEnemy.GetComponent<EnemyAI>().speed = Random.Range(1f, 3f);  // Override скорость

        // Destroy через 10 сек
        Destroy(newEnemy, 10f);
    }
}
```
*Применение*: В EnemySpawner — волны из prefab.

*Ссылка на документацию*: [Prefab Instantiation](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html).

## 💻 **Организация Кода: Папки, Менеджеры и Лучшие Практики**
Организация — чтобы код был читаемым: папки, singletons, события.  

- **Папки**: Assets/Scripts/Player/, Enemies/, Managers/.  
- **Singleton**: Один экземпляр (GameManager для очков).  
- **События**: UnityEvents или C# delegates для decoupling (стрельба вызывает OnShoot).  

**Распространённые Ошибки**:  
- Global MonoBehaviour: Используй static вместо — избегай FindObjectOfType.  
- Magic Numbers: Выноси в [SerializeField] для Inspector.  

**Пример в Шутере: GameManager Singleton для Очков Между Сценами**:
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public static GameManager Instance;  // Singleton
    public int score = 0;
    public bool gameOver = false;

    void Awake()
    {
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);  // Сохрани между сценами
        }
        else
        {
            Destroy(gameObject);
        }
    }

    public void AddScore(int points)
    {
        score += points;
        // Обнови UI: FindObjectOfType<DynamicHUD>().AddScore(points);  // Или событие
    }

    void OnApplicationQuit()
    {
        // Сохрани прогресс (PlayerPrefs)
        PlayerPrefs.SetInt("HighScore", score);
    }
}
```
*Применение*: Добавь пустой GameObject "GameManager" — очки сохраняются при рестарте.

**Лучшие Практики**:
- **Namespace**: using Scripts.Player; для избежания конфликтов.  
- **Comments**: /// XML для IntelliSense.  
- **Version Control**: Git ignore /Library и /Temp.  

*Ссылка на документацию*: [Best Practices for Code Organization](https://docs.unity3d.com/Manual/bestpractice-scripting.html).

## 🎮 **Практические Примеры Организации в Твоём Шутере**
Готовый код — рефакторинг.

#### **Пример 1: Мульти-Сцена Менеджер (Переходы + Сохранение)**
```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneOrganizer : MonoBehaviour
{
    public void LoadLevel(string levelName)
    {
        // Анимация fade-out перед загрузкой
        StartCoroutine(FadeAndLoad(levelName));
    }

    IEnumerator FadeAndLoad(string sceneName)
    {
        // CanvasGroup fade to 0
        CanvasGroup fadePanel = GameObject.Find("FadePanel").GetComponent<CanvasGroup>();
        fadePanel.DOFade(1f, 0.5f);

        yield return new WaitForSeconds(0.5f);
        SceneManager.LoadScene(sceneName);
    }
}
```
*Применение*: В меню — LoadLevel("Arena").

#### **Пример 2: Префаб с Override (Враг с Вариациями)**
```csharp
public class VariantEnemy : MonoBehaviour
{
    [SerializeField] private EnemyType type;  // Enum: Basic, Fast, Tank

    void Start()
    {
        switch (type)
        {
            case EnemyType.Basic:
                GetComponent<EnemyAI>().speed = 2f;
                GetComponent<EnemyHealth>().maxHealth = 50;
                break;
            case EnemyType.Fast:
                GetComponent<EnemyAI>().speed = 5f;
                GetComponent<EnemyHealth>().maxHealth = 30;
                break;
        }
        // Apply изменения в prefab
    }
}

public enum EnemyType { Basic, Fast, Tank }
```
*Применение*: В prefab Enemy — override type в Inspector при спавне.

#### **Пример 3: Код-Организатор (События для Decoupling)**
```csharp
using UnityEngine;
using UnityEngine.Events;

public class GameEvents : MonoBehaviour
{
    public static GameEvents Instance;
    public UnityEvent<int> onScoreChanged;  // Событие для очков
    public UnityEvent onEnemyKilled;

    void Awake()
    {
        Instance = this;
    }

    public void ScoreChanged(int newScore)
    {
        onScoreChanged.Invoke(newScore);  // UI обновляется
    }

    public void EnemyKilled()
    {
        onEnemyKilled.Invoke();  // +очки, спавн следующего
    }
}
```
*Применение*: В BulletDamage: GameEvents.Instance.ScoreChanged(10); — UI реагирует автоматически.

## 💡 **Продвинутые Фишки Организации**
- **Addressables**: Для больших проектов — загружай ассеты асинхронно.  
- **ScriptableObjects**: Данные вне кода (enemy stats в SO).  
- **Git LFS**: Для больших файлов (модели, звуки).  

**Эксперимент**: Создай 2 сцены (Menu, Arena), prefab врага — спавнь в Arena из Menu.

*Ссылка на продвинутые примеры*: [Prefab Variants and Scenes](https://learn.unity.com/tutorial/prefabs-and-scene-management).

## Заключение: Код — Как Кирпичи Замка 🧱
Сцены разделяют игру, префабы повторяют, организация — упрощает. В шутере это значит чистый спавн волн и плавные переходы. Освой — и масштабируй проекты!  

**Что Далее?**  
- Перейди к [Отладка и Сборка](./DebugBuild.md) — финальный тест.  
- Вопросы: [Unity Learn: Scenes and Prefabs](https://learn.unity.com/tutorial/scenes-and-prefabs).  

Ты организовал хаос — теперь строй империи! Продолжай, архитектор. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*