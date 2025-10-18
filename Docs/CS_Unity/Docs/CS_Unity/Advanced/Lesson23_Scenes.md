
# 🗺️ Урок 23: Сцены — Переходы между Уровнями

Привет, юный картограф арены! 💻 Добро пожаловать на десятый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **сцены** в Unity, чтобы создать несколько уровней в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты добавишь главное меню и второй уровень, а также реализуешь переходы между ними! Готов исследовать новые земли? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool` из [Урок 22: PlayerPrefs](../Advanced/Lesson22_Saving.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scenes в Assets — твоя карта миров!  

**Предупреждение**: Работа со сценами требует настройки **Build Settings** и правильного управления объектами. Сохраняй код (**Ctrl+S**) и сцены перед тестом, иначе переходы не сработают! Если сцены не загружаются, проверь индексы в **Build Settings**. Врубай Play Mode и исследуй уровни!

Готов создать новые миры? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Сцены?**
**Сцены** в Unity — это контейнеры для игровых миров (уровни, меню).  
- **SceneManager**: Управляет загрузкой сцен (`SceneManager.LoadScene`).  
- **Build Settings**: Список сцен для сборки игры.  
- **DontDestroyOnLoad**: Сохраняет объекты между сценами.  

В твоём шутере ты создашь главное меню и второй уровень с переходами.

*Ссылка на документацию*: [SceneManager](https://docs.unity3d.com/ScriptReference/SceneManagement.SceneManager.html).

## 🔄 **Зачем это Нужно?**
Сцены разделяют игру на уровни и меню, улучшая структуру. В этом квесте ты:  
- Создашь главное меню с кнопкой старта.  
- Добавишь второй уровень с новым окружением.  
- Реализуешь переходы между сценами.  

**Почему это круто?**  
- **Структура**: Разделение игры на уровни.  
- **Интерфейс**: Меню делает игру профессиональной.  
- **Для шутера**: Уровни добавляют разнообразие!  

**Типичные ошибки новичков**:  
- Забыл добавить сцены в **Build Settings**.  
- Неправильный индекс сцены в `LoadScene`.  
- Уничтожение объектов, которые должны сохраняться.  

## ⚙️ **Квест: Создай уровни**

### Уровень 1: Главное меню  
1. **Создай сцену меню:**  
   - В **Project** → `Assets/Scenes`, создай новую сцену, назови `MainMenu`.  
   - Добавь **Canvas** (Scale With Screen Size), **Button** и **TextMeshProUGUI** для заголовка.  
   - В **TextMeshProUGUI** напиши: `Unity Top-Down Shooter Quest`.  
   - В **Button** добавь текст: `Start Game`.  

2. **Создай скрипт `MenuController`:**  
   - В **Project** создай **C# Script**, назови `MenuController`.  
   - Реализуй переход на первую сцену:  
     ```csharp
     using UnityEngine;
     using UnityEngine.SceneManagement;

     public class MenuController : MonoBehaviour
     {
         public void StartGame()
         {
             SceneManager.LoadScene(1); // Индекс игровой сцены
             Debug.Log("Переход на первый уровень!");
         }
     }
     ```

3. **Настрой кнопку:**  
   - В **Hierarchy** выбери `Button`, в **Inspector** в `OnClick` добавь `MenuController` → `StartGame`.  
   - Сохрани сцену (**Ctrl+S**).  

4. **Обнови `Build Settings`:**  
   - Открой **File** → **Build Settings**.  
   - Перетащи `MainMenu` (индекс 0) и текущую игровую сцену (например, `GameScene`, индекс 1).  

5. **Настрой и протестируй:**  
   - Нажми **Play** в `MainMenu` — кнопка переводит на игровой уровень!  

### Уровень 2: Второй уровень  
1. **Создай вторую игровую сцену:**  
   - В **Project** → `Assets/Scenes`, создай сцену, назови `Level2`.  
   - Скопируй объекты из `GameScene` (`Player`, `EnemySpawner`, `BonusSpawner`, `GameManager`, `Main Camera`, `Canvas`).  
   - Добавь уникальный фон (например, новый материал на плоскости).  

2. **Обнови `GameManager`:**  
   - Добавь переход на следующий уровень при наборе 1000 очков:  
     ```csharp
     void Update()
     {
         statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}, Очки: {totalScore}, Волна: {enemySpawner.GetWaveNumber()}";
         if (totalScore >= 1000 && SceneManager.GetActiveScene().buildIndex == 1)
         {
             SceneManager.LoadScene(2); // Переход на Level2
             Debug.Log("Переход на второй уровень!");
         }
     }
     ```

3. **Обнови `Build Settings`:**  
   - Добавь `Level2` (индекс 2) в **Build Settings**.  

4. **Настрой и протестируй:**  
   - Нажми **Play** в `GameScene`, набери 1000 очков — переход на `Level2`!  

### Уровень 3: Возврат в меню  
1. **Обнови `GameManager`:**  
   - Добавь возврат в меню по клавише `M`:  
     ```csharp
     void Update()
     {
         statsText.text = $"Враги: {enemies.Count}, Бонусы: {bonuses.Count}, Очки: {totalScore}, Волна: {enemySpawner.GetWaveNumber()}";
         if (totalScore >= 1000 && SceneManager.GetActiveScene().buildIndex == 1)
         {
             SceneManager.LoadScene(2);
             Debug.Log("Переход на второй уровень!");
         }
         if (Input.GetKeyDown(KeyCode.M))
         {
             SceneManager.LoadScene(0);
             Debug.Log("Возврат в главное меню!");
         }
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, нажми `M` — возврат в `MainMenu`!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Кнопка в `MainMenu` и поля в `GameManager`.  
- В **Scene/Game**: Переходы между меню, `GameScene` и `Level2`.  
- В **Console**: Сообщения о переходах.  

## 💡 **Квесты: Прокачай уровни!**
1. **Базовый квест**:  
   - Измени порог перехода на `Level2` до 500 очков.  
   - Проверь: переход происходит раньше!  

2. **Квест на меню**:  
   - Добавь кнопку `Quit` в `MainMenu`:  
     ```csharp
     public void QuitGame()
     {
         Application.Quit();
         Debug.Log("Игра закрыта!");
     }
     ```  
   - Проверь: кнопка закрывает игру (в редакторе логирует).  

3. **Квест на уровень**:  
   - В `Level2` увеличь `spawnInterval` в `EnemySpawner` до 3.0f.  
   - Проверь: враги появляются реже!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь UI с номером уровня:  
     ```csharp
     [SerializeField] private TextMeshProUGUI levelText;

     void Start()
     {
         totalScore = PlayerPrefs.GetInt("TotalScore", 0);
         scoreText.text = "Очки: " + totalScore;
         int savedWave = PlayerPrefs.GetInt("WaveNumber", 1);
         enemySpawner.SetWaveNumber(savedWave);
         levelText.text = $"Уровень: {SceneManager.GetActiveScene().buildIndex}";
     }
     ```  
   - Проверь: UI показывает номер уровня!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если переходы не работают:  
  - Сохранил ли сцены и код (**Ctrl+S**)?  
  - Проверь индексы в **Build Settings**.  
  - Уничтожаются ли объекты с `DontDestroyOnLoad`?  
- **Хочешь эпичности?** В `MenuController` добавь:  
  ```csharp
  Debug.Log("Кнопка старта нажата!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Сцены не загружаются?** Проверь `SceneManager.LoadScene`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Сцены структурируют игру, добавляя меню и уровни. Это ключ к профессиональной структуре!

## Заключение: Сцены — Твоя Карта Миров! 💻
Ты освоил сцены, добавив меню и уровни. Твой шутер стал многогранным! Следующий шаг — физика с Rigidbody. Продолжай, картограф кода!

**Что Далее?**  
- Перейди к [Физика — Rigidbody и Силы](../Advanced/Lesson24_Physics.md) — добавь физику.  
- Вопросы: [Unity Learn: Scenes](https://learn.unity.com/tutorial/scenes).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
