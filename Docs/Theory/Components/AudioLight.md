# 🔊 Анимация и Эффекты: Звук и Свет — Добавим Аудио к Анимациям

Привет, звукорежиссёр света! 🔊 Эта глава теории — твой полный гид по **звуку и свету в Unity с интеграцией анимаций**: от AudioSource (выстрелы, взрывы синхронизированные с клипами) до Light компонентов (динамические вспышки при анимациях боя). Мы оживим твой топ-даун шутер: анимация стрельбы + звук выстрела + свет от дула. Это сделает игру immersive — не просто визуал, а сенсорный опыт! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (из курса, с анимациями из Урока 6).  
- Бесплатные звуки/модели из Asset Store (ищи "free audio pack").  

**Предупреждение**: Звуки могут "клиппить" на высокой громкости — используй Audio Mixer для контроля. Свет жрёт GPU — лимитируй количество источников. Тестируй с наушниками в Play Mode для фидбека. Если аудио не синхронизировано — проверь Timing в Animator.  

Готов зажечь арену звуком? По компонентам, как по саундтреку! 📝

## 🎯 **Что Такое Звук и Свет в Unity и Зачем Они С Анимациями?**
Звук (Audio) и Свет (Lighting) — сенсорные слои: аудио даёт фидбек (bang при выстреле), свет — визуал (вспышка при анимации). Синхронизация: событие в Animator триггерит звук/свет.  

- **Для чего?**  
  - Аудио: Иммерсия — шаг героя + звук ходьбы, урон + grunt.  
  - Свет: Атмосфера — дуло мигает при shoot-анимации, взрыв освещает арену.  
  - Синхронизация: Анимация idle + ambient музыка, click + sound effect.  

**Основные Компоненты**:  
- **Audio**: AudioSource (источник), AudioListener (слушатель на камере).  
- **Light**: Directional/Point/Spot для теней и бликов.  

В твоём шутере: Анимация взрыва + AudioSource с boom + Point Light для вспышки — эффект полный! Без них — тихая, тёмная арена.  

**Как Начать?**: Add Component > Audio > AudioSource. Для света — 3D Object > Light.  

*Ссылка на официальную документацию*: [Audio Overview](https://docs.unity3d.com/Manual/Audio.html) и [Lighting Overview](https://docs.unity3d.com/Manual/LightingOverview.html).

## 🔄 **Как Работает Звук? (AudioSource + Синхронизация с Анимациями)**
Аудио обновляется в реальном времени, синхронизировано с Animator (Animation Events).  

- **AudioSource**: Играет клипы (.wav/ogg), с контролем pitch/volume.  
- **AudioListener**: На камере — "уши" игрока (3D звук по расстоянию).  
- **Синхронизация**: В Animation окне добавь Event на кадре — вызови PlaySound().  

**Распространённые Ошибки**:  
- Нет Listener — звук не слышен.  
- Clip null — добавь AudioClip в Inspector.  
- 2D vs 3D: Spatial Blend=0 для UI, 1 для мира.  

**Пример в Шутере**: AudioSource на герое — PlayOneShot при shoot-триггере в Animator.

## ⚙️ **Свойства AudioSource — Громкость, Питч и 3D Звук**
AudioSource — "динамик" объекта. Добавь к GameObject.

| Свойство | Описание | Пример в Шутере | Значение по Умолчанию |
|----------|----------|-----------------|-----------------------|
| **AudioClip** | Файл звука. | Shot.wav для выстрела. | None |
| **Volume** (float) | Громкость (0–1). | 0.7 — не оглушает. | 1 |
| **Pitch** (float) | Высота тона (0.5–3). | 1.2 — быстрый выстрел выше. | 1 |
| **Spatial Blend** (float) | 3D (1) vs 2D (0). | 1 — звук от дула. | 0 |
| **Play On Awake** (bool) | Авто-старт. | Off для эффектов. | Off |
| **Loop** (bool) | Повтор. | On для музыки. | Off |
| **Doppler Level** (float) | Эффект Доплера (для скорости). | 1 — звук меняется при движении. | 1 |

**Код для Настройки и Синхронизации** (прикрепи к герою, вызови из Animator Event):
```csharp
using UnityEngine;

public class AudioSync : MonoBehaviour
{
    public AudioSource audioSource;
    public AudioClip shootClip, hitClip;

    // Вызов из Animation Event "PlayShootSound"
    public void PlayShootSound()
    {
        audioSource.pitch = Random.Range(0.9f, 1.1f);  // Вариация
        audioSource.PlayOneShot(shootClip, 0.8f);  // Громкость 80%
    }

    // Для 3D: Звук от позиции
    public void PlayHitSound(Vector3 position)
    {
        AudioSource.PlayClipAtPoint(hitClip, position, 1f);  // Глобальный звук
    }
}
```
*Применение*: В Animator на Shoot клипе — Event с методом PlayShootSound().

*Ссылка на документацию*: [AudioSource Properties](https://docs.unity3d.com/ScriptReference/AudioSource.html).

## 🌟 **Как Работает Свет? (Light Компоненты + Синхронизация)**
Свет рендерится в реальном времени (Baked для статичного), синхронизирован с анимациями (включение при триггере).  

- **Light**: Источники — Directional (солнце), Point (лампа), Spot (фонарь).  
- **Синхронизация**: В Animator Event — light.intensity = 2f на кадре вспышки.  

**Распространённые Ошибки**:  
- Слишком много источников — FPS падает (лимит 8 dynamic).  
- Нет Shadow — Edit > Project Settings > Quality > Shadows.  
- Baked Light — для статичных, но не для анимаций.  

**Пример в Шутере**: Point Light на дуле — intensity вспыхивает при shoot.

## ⚙️ **Свойства Light — Интенсивность, Цвет и Тени**
Light — "солнце" сцены. Добавь 3D Object > Light.

| Тип Света | Описание | Ключевые Свойства | Пример в Шутере |
|-----------|----------|-------------------|-----------------|
| **Directional** | Параллельный (солнце). | Intensity (яркость), Color, Shadow Type (Soft/Hard). | Основной свет арены (день). |
| **Point** | Оминidirectional (лампа). | Range (радиус), Intensity, Falloff (затухание). | Вспышка от взрыва (Range=5). |
| **Spot** | Конус (фонарь). | Spot Angle (ширина), Range, Intensity. | Дуло героя (Angle=30°). |

**Общие Свойства**: Shadow Strength (0–1), Cookie (текстура тени).  

**Код для Динамического Света** (прикрепи к объекту, синхронизируй с анимацией):
```csharp
using UnityEngine;

public class LightSync : MonoBehaviour
{
    private Light pointLight;
    public float maxIntensity = 5f;
    public float fadeTime = 0.5f;

    void Start()
    {
        pointLight = GetComponent<Light>();
        pointLight.intensity = 0f;  // Выключено
    }

    // Вызов из Animation Event "FlashLight"
    public void FlashLight()
    {
        pointLight.intensity = maxIntensity;
        StartCoroutine(FadeLight());
    }

    IEnumerator FadeLight()
    {
        yield return new WaitForSeconds(fadeTime);
        pointLight.intensity = 0f;  // Затухание
    }
}
```
*Применение*: На дуле героя — вспышка синхронно с shoot-анимацией.

*Ссылка на документацию*: [Light Component Properties](https://docs.unity3d.com/ScriptReference/Light.html).

## 💻 **Синхронизация Аудио/Света с Анимациями: Events и Coroutines**
Используй Animation Events для точной синхронизации.

- **Animation Event**: В Animation окне клипни на кадре > Add Event > Выбери метод (PlaySound).  

**Пример в Шутере: Полная Синхронизация Shoot (Анимация + Звук + Свет)**:
```csharp
using UnityEngine;
using System.Collections;

public class ShootSync : MonoBehaviour
{
    public AudioSource audioSource;
    public Light muzzleLight;
    public AnimationEvent shootEvent;  // Авто-добавится в Animator

    // Метод для Event в Animator
    public void OnShootEvent()
    {
        audioSource.PlayOneShot(shootClip);  // Звук
        StartCoroutine(LightFlash());  // Свет
    }

    IEnumerator LightFlash()
    {
        muzzleLight.intensity = 8f;
        yield return new WaitForSeconds(0.1f);
        muzzleLight.intensity = 0f;
    }

    // В Update для триггера
    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            GetComponent<Animator>().SetTrigger("Shoot");  // Запуск анимации
        }
    }
}
```
*Применение*: Event на 5-м кадре shoot-клипа — boom + flash.

*Ссылка на документацию*: [Animation Events](https://docs.unity3d.com/Manual/AnimationEvents.html).

## 🎮 **Практические Примеры Звука и Света в Твоём Шутере**
Готовый код — интегрируй.

#### **Пример 1: Аудио-Фидбек для Движения (Шаги + Ambient)**
```csharp
using UnityEngine;

public class MovementAudio : MonoBehaviour
{
    public AudioSource footstepSource;
    public AudioClip[] stepClips;  // Массив шагов
    private Animator animator;
    private float stepInterval = 0.5f;
    private float nextStepTime;

    void Start()
    {
        animator = GetComponent<Animator>();
        footstepSource.loop = false;
    }

    void Update()
    {
        float speed = animator.GetFloat("Speed");
        if (speed > 0.1f && Time.time > nextStepTime)
        {
            footstepSource.PlayOneShot(stepClips[Random.Range(0, stepClips.Length)]);
            nextStepTime = Time.time + stepInterval;
        }
    }
}
```
*Применение*: Шаги синхронно с walk-анимацией.

#### **Пример 2: Динамический Свет для Взрыва (Синхронно с Частицами)**
```csharp
using UnityEngine;
using System.Collections;

public class ExplosionLight : MonoBehaviour
{
    public Light explosionLight;
    public ParticleSystem explosionParticles;
    public float lightDuration = 1f;

    public void TriggerExplosion()
    {
        explosionParticles.Play();
        explosionLight.intensity = 10f;
        explosionLight.color = Color.yellow;
        StartCoroutine(FadeLight());
        GetComponent<AudioSource>().PlayOneShot(explosionClip);
    }

    IEnumerator FadeLight()
    {
        float elapsed = 0f;
        float startIntensity = explosionLight.intensity;

        while (elapsed < lightDuration)
        {
            elapsed += Time.deltaTime;
            explosionLight.intensity = Mathf.Lerp(startIntensity, 0f, elapsed / lightDuration);
            yield return null;
        }

        explosionLight.intensity = 0f;
    }
}
```
*Применение*: Вызови при смерти врага — свет + звук + частицы.

#### **Пример 3: Ambient Свет с Анимацией (День/Ночь Цикл)**
```csharp
using UnityEngine;

public class DayNightCycle : MonoBehaviour
{
    public Light directionalLight;
    public float cycleSpeed = 0.1f;

    void Update()
    {
        // Анимация цвета света (день -> ночь)
        float timeOfDay = (Time.time * cycleSpeed) % 1f;
        Color lightColor = Color.HSLToRGB(timeOfDay * 0.25f, 0.6f, 0.5f);  // От жёлтого к синему
        directionalLight.color = lightColor;
        directionalLight.intensity = Mathf.Lerp(0.5f, 1.5f, Mathf.Sin(timeOfDay * 2 * Mathf.PI));
    }
}
```
*Применение*: Фон арены меняет освещение — динамичная атмосфера.

## 💡 **Продвинутые Фишки Звука и Света**
- **Audio Mixer**: Группируй звуки (SFX, Music) — Edit > Project Settings > Audio.  
- **Light Probes**: Для динамических объектов — интерполирует baked свет.  
- **FMOD/ Wwise**: Для сложного аудио (адаптивное под события) — Asset Store.  

**Эксперимент**: Добавь AudioSource к пуле, Point Light к дулу — синхронизируй Event в Animator shoot-клипа.

*Ссылка на продвинутые примеры*: [Audio with Animation](https://learn.unity.com/tutorial/audio-and-animation).

## Заключение: Звук и Свет — Сенсорный Взрыв! 🔊
AudioSource + Light с анимациями — твои инструменты для "ощущений": выстрел гремит, вспышка ослепляет. В шутере это превратит арену в симфонию хаоса. Освой — и игра зазвучит!  

**Что Далее?**  
- Перейди к [Дополнительно: Сцены и Префабы](./ScenesPrefabs.md) — организуем код.  
- Вопросы: [Unity Learn: Audio and Lighting](https://learn.unity.com/tutorial/audio-and-lighting).  

Ты зажёг арену — теперь слушай аплодисменты! Продолжай, саундтрекер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*