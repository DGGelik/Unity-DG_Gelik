# 🌈 Дополнительно: Шейдеры и Материалы — Визуалы

Привет, визуальный алхимик! 🌈 Эта глава дополнительной теории — твой полный гид по **шейдерам и материалам в Unity**: от базовых материалов (цвета, текстуры) до шейдеров (кастомные эффекты, как glow на оружии или distortion от взрывов). Мы создадим визуалы для твоего топ-даун шутера: материалы для арены (металл с отражениями), шейдеры для пуль (трассеры с хвостом). Это шаг к графике AAA — от серых кубов к сияющим эффектам! Время на чтение: 25–35 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 2+).  
- Shader Graph (Window > Package Manager > Shader Graph — бесплатно).  

**Предупреждение**: Шейдеры — GPU-интенсивны, тест на слабом железе (Profiler > GPU Usage). Материалы на URP/HDRP — переключай Render Pipeline (Project Settings > Graphics). Если шейдер "розовый" — ошибка компиляции (Console). Экспериментируй в Material Inspector, сохраняй как prefab.  

Готов покрасить арену? По компонентам, как по палитре! 📝

## 🎯 **Что Такое Шейдеры и Материалы и Зачем Они Нужны?**
**Материалы** — "краска" для объектов: применяют текстуры, цвета к mesh. **Шейдеры** — "рецепты" рендеринга: как свет взаимодействует с материалом (стандартный Standard, кастом для glow).  

- **Для чего Материалы?**  
  - Визуал: Арена — бетон с текстурой, герой — металлический.  
  - Баланс: Alpha для прозрачности (призрачные враги).  

- **Для чего Шейдеры?**  
  - Эффекты: Трассеры пуль (линия света), bloom на взрывах.  
  - Оптимизация: Mobile шейдеры для лёгкости.  

В твоём шутере: Материал "ArenaMat" с roughness для реалистичного пола, шейдер "GunGlow" — дуло светится при анимации. Без них — плоские цвета, как в 90-х.  

**Как Начать?**: Create > Material, назначь шейдер (в Shader dropdown).  

*Ссылка на официальную документацию*: [Materials Overview](https://docs.unity3d.com/Manual/Material.html) и [Shaders Overview](https://docs.unity3d.com/Manual/Shaders.html).

## 🔄 **Как Работают Материалы? (Standard Shader и Свойства)**
Материалы — контейнеры шейдеров + текстур: применяй к Renderer.  

- **Standard Shader**: Универсальный (PBR — physically based rendering).  
- **Процесс**: Текстура → UV mapping → Шейдер рендерит пиксели.  

**Распространённые Ошибки**:  
- Неправильный шейдер — чёрный объект (Shader dropdown).  
- Текстура не импортирована — розовый (Import Settings > Texture Type).  
- UV разрыв — текстура искажается (проверь в модели).  

**Свойства Материала (в Inspector)**:
| Свойство | Описание | Пример в Шутере | Значение по Умолчанию |
|----------|----------|-----------------|-----------------------|
| **Shader** (dropdown) | Тип рендеринга. | Standard (URP/Lit). | Standard |
| **Albedo (Main Color/Texture)** | Базовый цвет/текстура. | Трава для арены (RGB + Alpha). | White |
| **Metallic** (float) | Металличность (0–1). | 0.8 для оружия (отражения). | 0 |
| **Smoothness** (float) | Гладкость (блики). | 0.9 для пуль (зеркальный). | 0.5 |
| **Normal Map** | Рельеф (без доп. геометрии). | Кирпичи на стенах. | None |
| **Emission** | Самосвечение. | Glow для взрывов (цвет + intensity). | Black |

**Код: Динамическое Изменение Материала** (прикрепи к объекту):
```csharp
using UnityEngine;

public class MaterialChanger : MonoBehaviour
{
    private Renderer objectRenderer;
    public Material damagedMaterial;  // Перетащи в Inspector

    void Start()
    {
        objectRenderer = GetComponent<Renderer>();
    }

    public void TakeDamageEffect()
    {
        // Сменить материал на повреждённый
        objectRenderer.material = damagedMaterial;

        // Восстановить через 2 сек
        Invoke("RestoreMaterial", 2f);
    }

    void RestoreMaterial()
    {
        objectRenderer.material = originalMaterial;  // Сохрани original в Start
    }

    // Эффект свечения
    public void GlowEffect(bool enable)
    {
        if (enable)
        {
            objectRenderer.material.SetColor("_EmissionColor", Color.yellow * 2f);
            objectRenderer.material.EnableKeyword("_EMISSION");
        }
        else
        {
            objectRenderer.material.DisableKeyword("_EMISSION");
        }
    }
}
```
*Применение*: При уроне — TakeDamageEffect() на враге, Glow для босса.

*Ссылка на документацию*: [Material Properties](https://docs.unity3d.com/Manual/StandardShaderMaterialProperties.html).

## 🌟 **Как Работают Шейдеры? (Shader Graph и Кастом)**
Шейдеры — код для GPU: вершинный (формы) + фрагментный (пиксели). Shader Graph — визуальный редактор (nodes как Lego).  

- **Shader Graph**: Drag nodes (Texture2D, Lerp) — генерит HLSL код.  
- **Процесс**: Master Node (PBR) → Sub Graph для модулей (glow).  

**Распространённые Ошибки**:  
- Graph не скомпилирован — розовый материал (кнопка Save Asset).  
- URP/HDRP несоответствие — шейдер не работает (Project Settings > Graphics > Scriptable Render Pipeline).  
- Текстуры не подключены — дефолтный цвет.  

**Свойства Шейдера (в Graph)**:
| Node | Описание | Пример в Шутере | Вывод |
|------|----------|-----------------|-------|
| **Texture 2D** | Текстура. | Concrete для арены. | Base Color |
| **Color** | Цвет. | Красный для врагов. | Emission |
| **Lerp** | Интерполяция. | Fade между idle/damaged. | Alpha |
| **Simple Noise** | Шум (distortion). | Волны от взрыва. | UV Offset |
| **PBR Master** | Выход (URP). | Intensity, Metallic. | Финальный рендер |

**Код: Создание и Применение Кастом Шейдера** (Shader Graph: New > URP > Lit Graph):
1. В Graph: Texture2D node → Sample Texture2D → Base Color в Master.  
2. Сохрани как "GlowShader".  
3. Material: Shader = Custom/GlowShader.  

**Простой HLSL Шейдер (Текст)**:
```hlsl
Shader "Custom/GlowShader"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _GlowColor ("Glow Color", Color) = (1,1,0,1)
        _GlowPower ("Glow Power", Range(0,5)) = 1
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 200

        CGPROGRAM
        #pragma surface surf Standard fullforwardshadows

        sampler2D _MainTex;
        fixed4 _GlowColor;
        float _GlowPower;

        struct Input
        {
            float2 uv_MainTex;
        };

        void surf (Input IN, inout SurfaceOutputStandard o)
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
            o.Emission = _GlowColor.rgb * _GlowPower;  // Свечение
            o.Metallic = 0;
            o.Smoothness = 0.8;
            o.Alpha = c.a;
        }
        ENDCG
    }
    FallBack "Diffuse"
}
```
*Применение*: Сохрани как .shader, назначь материалу — дуло светится.

*Ссылка на документацию*: [Shader Graph](https://docs.unity3d.com/Packages/com.unity.shadergraph@latest).

## 💻 **Практические Примеры Шейдеров и Материалов в Твоём Шутере**
Готовый код — визуализируй.

#### **Пример 1: Динамический Материал для Урона (Красный Flash)**
```csharp
using UnityEngine;

public class DamageMaterial : MonoBehaviour
{
    private Renderer rend;
    private Material originalMat;
    public Material damageMat;  // Красный вариант

    void Start()
    {
        rend = GetComponent<Renderer>();
        originalMat = rend.material;  // Клон для неразрушения
    }

    public void ApplyDamageVisual(float duration = 0.5f)
    {
        rend.material = damageMat;
        Invoke("RestoreMaterial", duration);
    }

    void RestoreMaterial()
    {
        rend.material = originalMat;
    }

    // Шейдер-эффект: Изменить свойство
    public void GlowOnHit()
    {
        rend.material.SetFloat("_GlowPower", 3f);  // В шейдере
        Invoke("ResetGlow", 0.3f);
    }

    void ResetGlow()
    {
        rend.material.SetFloat("_GlowPower", 0f);
    }
}
```
*Применение*: В OnTriggerEnter — ApplyDamageVisual().

#### **Пример 2: Shader Graph для Трассеров Пуль (Хвост Света)**
1. New Shader Graph > Unlit Graph (для линий).  
2. Nodes: Position → Distance (от start) → Lerp (fade) → Unlit Master (Emission).  
3. Сохрани "TracerShader".  
4. Material: Shader = Custom/TracerShader, назначь пуле.  

**Код для Пулинга с Материалом**:
```csharp
public class TracerBullet : MonoBehaviour
{
    private TrailRenderer trail;  // Хвост (компонент)
    public Material tracerMaterial;  // С шейдером

    void Start()
    {
        trail = GetComponent<TrailRenderer>();
        trail.material = tracerMaterial;  // Светящийся хвост
        trail.time = 0.5f;  // Длина следа
        trail.startWidth = 0.1f;
    }

    void OnTriggerEnter(Collider other)
    {
        // Уничтожь с эффектом
        trail.Clear();  // Убери хвост
        Destroy(gameObject);
    }
}
```
*Применение*: Пули оставляют светящийся след.

#### **Пример 3: Материалы для Атмосферы (День/Ночь)**
```csharp
using UnityEngine;

public class AtmosphereMaterials : MonoBehaviour
{
    public Material skyMaterial;  // Skybox материал
    public Light directionalLight;
    public float cycleTime = 60f;  // 1 мин цикл

    void Update()
    {
        float time = Time.time / cycleTime;
        Color skyColor = Color.HSVToRGB(time, 0.5f, 0.5f);  // От синего к оранжевому

        RenderSettings.skybox.SetColor("_Tint", skyColor);  // Небо
        directionalLight.color = skyColor * 1.5f;  // Свет солнца

        // Материал арены: Тени меняются
        RenderSettings.ambientLight = skyColor * 0.3f;
    }
}
```
*Применение*: Арена "дышит" — визуал меняется.

## 💡 **Продвинутые Фишки Шейдеров и Материалов**
- **URP/HDRP**: Universal для мобильных, High Definition для PC (Project Settings > Graphics).  
- **Substances**: Procedural текстуры (Asset Store).  
- **Compute Shaders**: Для GPU-вычислений (частицы).  

**Эксперимент**: Создай Material с Standard шейдером, добавь Emission — объект светится. Прикрепи к пуле.

*Ссылка на продвинутые примеры*: [Shader Graph Advanced](https://learn.unity.com/tutorial/shader-graph-advanced).

## Заключение: Визуалы — Душа Шутера 🌈
Материалы красят, шейдеры сияют — твоя арена из серости в эпик. В шутере это трассеры + glow = зрелище. Освой — и графика на уровне!  

**Что Далее?**  
- Перейди к [New Input System](./NewInputSystem.md) — современный ввод.  
- Вопросы: [Unity Learn: Shaders and Materials](https://learn.unity.com/tutorial/shaders-materials).  

Ты визуализировал — теперь сияй! Продолжай, шейдерман. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*