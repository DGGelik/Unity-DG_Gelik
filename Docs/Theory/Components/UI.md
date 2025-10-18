# 📱 UI и Интерфейс: Волшебные Экраны в Unity

Привет, дизайнер интерфейсов! 📱 Эта глава теории — твой полный гид по **UI элементам в Unity**: от Canvas (полотно для экрана) до кнопок, текста и слайдеров. UI — это "надстройка" над игрой: HUD с очками, меню, кнопки паузы. Мы разберём, как они работают, свойства, события, код-примеры и интеграцию с твоим топ-даун шутером (обновление здоровья, кнопка рестарта). Это сделает игру удобной и профессиональной! Время на чтение: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (из курса, с ареной и HUD из Урока 4).  
- TextMeshPro (импортируй: Window > TextMeshPro > Import TMP Essentials).  

**Предупреждение**: UI рендерится отдельно от сцены — используй Canvas Scaler для адаптации под экраны. Если элементы "дергаются" — проверь Anchors в RectTransform. Тестируй на разных разрешениях (Game View > Free Aspect).  

Готов нарисовать экран? По элементам, как по макету! 📝

## 🎯 **Что Такое UI в Unity и Зачем Оно Нужно?**
Unity UI (uGUI) — система для создания интерфейсов: кнопки, текст, изображения на экране. Основа — **Canvas** (виртуальный слой поверх 3D-мира).  

- **Для чего?**  
  - HUD: Здоровье, очки, ammo в твоём шутере.  
  - Меню: Старт, пауза, рестарт.  
  - Адаптивность: Элементы подстраиваются под мобильный/PC.  
  - События: Клик кнопки → стрельба или загрузка сцены.  

**Основные Компоненты**:  
- **Canvas**: Контейнер.  
- **RectTransform**: Transform для UI (anchors, pivot).  
- **Image/Text/Button/Slider**: Визуалы и интерактив.  

В твоём шутере: Canvas с HUD обновляется скриптом — очки растут при убийстве. Без UI игра "немая".  

**Как Начать?**: Правой кнопкой в Hierarchy > UI > Canvas.  

*Ссылка на официальную документацию*: [Unity UI System](https://docs.unity3d.com/Manual/UISystem.html).

## 🔄 **Как Работает UI? (Canvas и RectTransform)**
UI обновляется в **Update()**, но события — асинхронно (OnClick).  

- **Canvas**: "Экран" — Render Mode (Overlay для HUD, World Space для 3D-UI). Scaler — адаптация.  
- **RectTransform**: UI-версия Transform — позиция по якорям (Anchors: углы привязки к Canvas). Pivot — центр элемента.  

**Распространённые Ошибки**:  
- Anchors не настроены — UI "уплывает" при изменении экрана.  
- Нет EventSystem (Unity добавляет автоматически) — кнопки не кликабельны.  
- TextMeshPro не импортирован — текст базовый и устаревший.  

**Пример в Шутере**: Canvas Overlay для HUD — текст "Score: 0" привязан к Top-Left (Anchor: Top-Left).

## ⚙️ **Ключевые UI Элементы и Их Свойства**
Вот основные — добавь через Hierarchy > UI > [Элемент].  

| Элемент | Описание | Ключевые Свойства | Пример в Шутере |
|---------|----------|-------------------|-----------------|
| **Canvas** | Полотно. | Render Mode (Overlay), Scaler (Scale With Screen Size), Reference Resolution (1920x1080). | HUD поверх арены. |
| **RectTransform** (у всех UI) | Позиционирование. | Anchors (Top-Left для очков), Pivot (0.5,0.5 центр), Pos X/Y (отступы). | Кнопка рестарта в углу. |
| **Image** | Изображение/спрайт. | Source Image (спрайт), Color (RGBA), Raycast Target (блокирует клики). | Иконка здоровья (сердце). |
| **Text (TextMeshPro)** | Текст. | Text (строка), Font Size, Alignment, Color. | "Ammo: 30" — обновляется скриптом. |
| **Button** | Кликабельная кнопка. | Text Child (надпись), Image (фон), OnClick() (события). | "Pause" — останавливает время. |
| **Slider** | Ползунок. | Min/Max Value, Value (текущее), Handle Image (ползунок). | Полоска здоровья (0–100). |
| **Input Field** | Поле ввода. | Text Component, Placeholder, Content Type (число/текст). | Имя игрока в меню. |

**Код для Настройки** (прикрепи к Canvas для динамики):
```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class UIInitializer : MonoBehaviour
{
    public TextMeshProUGUI scoreText;
    public Slider healthSlider;
    public Button startButton;

    void Start()
    {
        scoreText.text = "Score: 0";
        healthSlider.maxValue = 100;
        healthSlider.value = 100;

        // Событие кнопки
        startButton.onClick.AddListener(() => Debug.Log("Game Started!"));
    }
}
```

*Ссылка на документацию*: [UI Components](https://docs.unity3d.com/Manual/UIElements.html).

## 💻 **События UI: OnClick, OnValueChanged и Скриптинг**
UI реагирует на взаимодействия — используй события в Inspector или код.

- **OnClick (Button)**: Вызов метода при клике.  
- **OnValueChanged (Slider/Text)**: При изменении.  

**Пример в Шутере: Кнопка Рестарта и Обновление HUD**:
```csharp
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.SceneManagement;
using TMPro;

public class ShooterUI : MonoBehaviour
{
    public Button restartButton;
    public TextMeshProUGUI ammoText;
    public Slider healthBar;
    private int currentAmmo = 30;

    void Start()
    {
        // Событие кнопки
        restartButton.onClick.AddListener(RestartGame);

        // Слайдер для здоровья
        healthBar.onValueChanged.AddListener(OnHealthChanged);
    }

    void UpdateAmmo(int ammo)
    {
        currentAmmo = ammo;
        ammoText.text = "Ammo: " + currentAmmo + "/30";
    }

    void OnHealthChanged(float value)
    {
        Debug.Log("Health changed to: " + value);
    }

    void RestartGame()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().name);  // Перезагрузка уровня
    }
}
```
*Применение*: Прикрепи к Canvas — клик на кнопку рестартует арену, слайдер реагирует на урон.

*Ссылка на документацию*: [UI Events](https://docs.unity3d.com/Manual/script-UIEvents.html).

## 🎮 **Практические Примеры UI в Твоём Шутере**
Готовый код — интегрируй в HUD.

#### **Пример 1: Динамический HUD (Очки + Здоровье)**
```csharp
public class DynamicHUD : MonoBehaviour
{
    public TextMeshProUGUI scoreText;
    public Image healthIcon;  // Сердечко
    public Slider ammoSlider;

    private int score = 0;

    public void AddScore(int points)
    {
        score += points;
        scoreText.text = "Score: " + score;
    }

    public void UpdateHealthIcon(float healthPercent)
    {
        // Заполни иконку по проценту (fill amount)
        healthIcon.fillAmount = healthPercent / 100f;
        healthIcon.color = healthPercent > 50 ? Color.green : Color.red;
    }

    public void UpdateAmmo(float ammoPercent)
    {
        ammoSlider.value = ammoPercent;
    }
}
```
*Применение*: Вызови AddScore при убийстве — очки растут, иконка краснеет при уроне.

#### **Пример 2: Пауза Меню с Кнопками**
```csharp
public class PauseMenu : MonoBehaviour
{
    public GameObject pausePanel;  // Панель с UI
    private bool isPaused = false;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Escape))
        {
            if (isPaused)
                Resume();
            else
                Pause();
        }
    }

    public void Pause()
    {
        Time.timeScale = 0f;  // Остановить игру
        pausePanel.SetActive(true);
        isPaused = true;
    }

    public void Resume()
    {
        Time.timeScale = 1f;
        pausePanel.SetActive(false);
        isPaused = false;
    }

    public void QuitGame()
    {
        Application.Quit();  // Выход (в билде)
    }
}
```
*Применение*: Добавь кнопки Resume/Quit на панель — Esc для паузы.

#### **Пример 3: Адаптивный UI (Для Разных Экранов)**
```csharp
public class AdaptiveUI : MonoBehaviour
{
    public RectTransform hudPanel;
    public CanvasScaler canvasScaler;

    void Start()
    {
        // Адаптация под экран
        canvasScaler.uiScaleMode = CanvasScaler.ScaleMode.ScaleWithScreenSize;
        canvasScaler.referenceResolution = new Vector2(1920, 1080);

        // Центрируй панель
        hudPanel.anchorMin = new Vector2(0, 0);
        hudPanel.anchorMax = new Vector2(1, 1);
        hudPanel.anchoredPosition = Vector2.zero;
    }
}
```
*Применение*: HUD всегда на месте на PC/мобиле.

## 💡 **Продвинутые Фишки UI**
- **Anchors и Layout Groups**: Авто-размещение (Horizontal Layout для меню).  
- **EventSystem**: Для тач/геймпада — Unity добавляет автоматически.  
- **UGUI vs UIElements**: UGUI для простоты, UIElements (новое) для производительности.  

**Эксперимент**: Создай Canvas, добавь Button с OnClick на Debug.Log — кликни в Play!

*Ссылка на продвинутые примеры*: [Advanced UI](https://learn.unity.com/tutorial/advanced-ui).

## Заключение: UI — Лицо Твоей Игры 📱
Canvas, Text, Button — это "окна" в твой шутер: HUD информирует, меню управляет. Освой — и игра засияет!  

**Что Далее?**  
- Перейди к [Анимация и Эффекты](./Animation.md) — оживим UI.  
- Вопросы: [Unity Learn: UI Design](https://learn.unity.com/tutorial/ui-design).  

Ты нарисовал интерфейс — теперь кликай к славе! Продолжай, пиксельмейстер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*