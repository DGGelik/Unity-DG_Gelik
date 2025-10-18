# 📦 Полный Список GameObjects в Unity: Примитивы и Создание

Привет, обджект-строитель! 📦 Эта глава теории — твой полный справочник по **GameObjects в Unity**: от базовых примитивов (Cube, Sphere) до UI-элементов и эффектов, которые создаёшь через меню Create (Hierarchy > Create Empty или 3D Object). GameObjects — "кирпичики" сцены: добавь компоненты (из предыдущей главы), и они оживают. Я категоризировал по меню (3D, 2D, UI, Effects), с описаниями, свойствами и примерами для твоего топ-даун шутера. Это не все возможные (импорт FBX добавит), но покрывает встроенные (Unity 2022.3+). Время на чтение: 15–20 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity.  
- Hierarchy: Правой кнопкой > 3D Object / UI / Effects.  

**Предупреждение**: Примитивы — стартовые, редактируй mesh (ProBuilder). Не перегружай сцену (1000+ объектов — лаги). Для кастом — Import Model. Тестируй в Play — Gizmos показывают форму.  

Готов создавать? По категориям, как по меню! 📝

## 🎯 **Что Такое GameObjects и Зачем Их Создавать?**
**GameObject** — контейнер: пустой + компоненты (Transform по умолчанию). Примитивы — готовые с mesh/renderer.  

- **Для чего?**  
  - Прототип: Cube как стена арены.  
  - UI: Button для меню.  
  - Эффекты: Particle System для взрыва.  

В твоём шутере: Sphere как пуля, Plane как пол, Canvas для HUD. Без них — пустая сцена.  

**Как Создать?**: Hierarchy > Правой > 3D Object / UI / etc. Или код: new GameObject("Name").  

*Ссылка на официальную документацию*: [GameObject Overview](https://docs.unity3d.com/Manual/GameObjects.html).

## 🏗️ **Категория 1: 3D Objects (Примитивы)**
Базовые формы — с Mesh Filter/Renderer.

| GameObject | Описание | Ключевые Свойства (Transform) | Пример в Шутере | Ссылка |
|------------|----------|-------------------------------|-----------------|--------|
| **Cube** | Куб. | Scale для размера. | Препятствие на арене. | [Cube](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Sphere** | Сфера. | Scale для радиуса. | Пуля или шар-враг. | [Sphere](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Capsule** | Капсула. | Scale (Height/Width). | Герой (без углов). | [Capsule](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Cylinder** | Цилиндр. | Scale (Height/Radius). | Колонна или башня. | [Cylinder](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Plane** | Плоскость. | Scale для размера. | Пол арены. | [Plane](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Quad** | Квадрат. | Scale для размера. | UI-плитка или панель. | [Quad](https://docs.unity3d.com/Manual/Primitive-GameObjects.html) |
| **Terrain** | Ландшафт. | Terrain Data (Heightmap). | Процедурная арена (bake). | [Terrain](https://docs.unity3d.com/Manual/terrain-UsingTerrains.html) |

## 🎨 **Категория 2: 2D Objects**
Для 2D-элементов (если портируешь шутер).

| GameObject | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|------------|----------|-------------------|-----------------|--------|
| **Sprite** | 2D спрайт. | Sprite Renderer (Sprite). | 2D-иконка оружия. | [Sprite](https://docs.unity3d.com/Manual/2d-sprites.html) |
| **Tilemap** | Картa тайлов. | Tilemap Renderer, Tilemap Collider. | 2D-арена из тайлов. | [Tilemap](https://docs.unity3d.com/Manual/Tilemap.html) |

## 🖥️ **Категория 3: UI Elements**
Интерфейс (Canvas child).

| GameObject | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|------------|----------|-------------------|-----------------|--------|
| **Canvas** | Полотно UI. | Render Mode, Scaler. | HUD контейнер. | [Canvas](https://docs.unity3d.com/Manual/UICanvas.html) |
| **Button** | Кнопка. | Text Child, OnClick. | Рестарт. | [Button](https://docs.unity3d.com/Manual/script-Button.html) |
| **Image** | Изображение. | Source Image, Color. | Иконка здоровья. | [Image](https://docs.unity3d.com/Manual/script-Image.html) |
| **Text** | Текст. | Text, Font. | Score label. | [Text](https://docs.unity3d.com/Manual/script-Text.html) |
| **TextMeshPro - Text (UI)** | Продвинутый текст. | Font Asset, Auto Size. | Ammo с эффектами. | [TMP_Text](https://docs.unity3d.com/Packages/com.unity.textmeshpro@latest) |
| **Slider** | Ползунок. | Min/Max Value. | Здоровье бар. | [Slider](https://docs.unity3d.com/Manual/script-Slider.html) |
| **Toggle** | Переключатель. | Is On, Group. | Музыка вкл/выкл. | [Toggle](https://docs.unity3d.com/Manual/script-Toggle.html) |
| **Input Field** | Ввод текста. | Content Type, Placeholder. | Имя в меню. | [InputField](https://docs.unity3d.com/Manual/script-InputField.html) |
| **Dropdown** | Список. | Options, Value. | Выбор уровня. | [Dropdown](https://docs.unity3d.com/Manual/script-Dropdown.html) |
| **Scroll View** | Скролл. | Content, Viewport. | Список ачивок. | [ScrollRect](https://docs.unity3d.com/Manual/script-ScrollView.html) |
| **Scrollbar** | Бар скролла. | Handle Rect. | В Scroll View. | [Scrollbar](https://docs.unity3d.com/Manual/script-Scrollbar.html) |

## 💥 **Категория 4: Effects (Эффекты)**
Визуальные эффекты.

| GameObject | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|------------|----------|-------------------|-----------------|--------|
| **Particle System** | Частицы. | Emission, Shape, Renderer. | Взрыв от пули. | [ParticleSystem](https://docs.unity3d.com/Manual/ParticleSystems.html) |
| **Light** (Directional/Point/Spot) | Свет. | Type, Intensity, Color. | Вспышка дула. | [Light](https://docs.unity3d.com/Manual/LightingOverview.html) |
| **Flare** | Линзовый блик. | Flare (asset), Strength. | Блик от солнца. | [LensFlare](https://docs.unity3d.com/Manual/class-LensFlare.html) |
| **Wind Zone** | Ветер. | Mode, Main, Turbulence. | Толчки листьев. | [WindZone](https://docs.unity3d.com/Manual/class-WindZone.html) |

## 📱 **Категория 5: Другие (Light, Camera, Terrain и т.д.)**
Разное.

| GameObject | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|------------|----------|-------------------|-----------------|--------|
| **Camera** | Камера. | Field of View, Clear Flags. | Вид сверху. | [Camera](https://docs.unity3d.com/Manual/class-Camera.html) |
| **Empty** | Пустой объект. | — | Менеджер (скрипты). | [Empty](https://docs.unity3d.com/Manual/GameObjects.html) |
| **Directional Light** | Параллельный свет. | Intensity, Color. | Солнце арены. | [DirectionalLight](https://docs.unity3d.com/Manual/Lights.html) |
| **Point Light** | Точечный свет. | Range, Intensity. | Взрыв. | [PointLight](https://docs.unity3d.com/Manual/Lights.html) |
| **Spot Light** | Прожектор. | Spot Angle, Range. | Фонарь. | [SpotLight](https://docs.unity3d.com/Manual/Lights.html) |
| **Area Light** | Плоский свет. | Width, Height. | Комнатное освещение. | [AreaLight](https://docs.unity3d.com/Manual/Lights.html) |
| **Reflection Probe** | Отражения. | Type, Refresh Mode. | Отражения в воде. | [ReflectionProbe](https://docs.unity3d.com/Manual/class-ReflectionProbe.html) |
| **Light Probe Group** | Проба света. | Probe Positions. | Динамические тени. | [LightProbeGroup](https://docs.unity3d.com/Manual/LightProbes.html) |
| **Terrain** | Ландшафт. | Terrain Layers, Trees. | Холмистая арена. | [Terrain](https://docs.unity3d.com/Manual/Terrain.html) |

## 💡 **Продвинутые Фишки GameObjects**
- **Create in Code**: `GameObject go = new GameObject("Bullet"); go.AddComponent<Rigidbody>();`.  
- **Prefab Variant**: От prefab — кастом версии.  
- **Dynamic Creation**: Instantiate для runtime (пули).  

**Эксперимент**: Создай Cube, добавь Rigidbody — толкни в Play.

*Ссылка на полный список*: [Create Menu](https://docs.unity3d.com/Manual/GameObject.html).

## Заключение: GameObjects — Кирпичики Твоего Мира! 📦
Примитивы — старт, UI/эффекты — polish. В шутере комбинируй (Plane + Material для арены) — и сцена живая. Освой — и создавай!  

**Что Далее?**  
- Вернись к урокам — используй примитивы.  
- Вопросы: [Unity Learn: GameObjects](https://learn.unity.com/tutorial/gameobjects).  

Ты собрал обджекты — теперь мир твой! Продолжай, билдер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*