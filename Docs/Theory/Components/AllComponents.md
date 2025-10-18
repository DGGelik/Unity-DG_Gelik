# 🎯 Полный Список Компонентов для GameObject в Unity Inspector

Привет, компонент-хантер! 🎯 Эта глава — твой полный справочник по **всем компонентам Unity**, которые можно добавить к GameObject через Inspector (Add Component). Unity имеет 100+ компонентов — от базовых (Transform) до продвинутых (NavMeshAgent). Я категоризировал их для удобства, с описаниями, свойствами, примерами в твоём топ-даун шутере и ссылками на docs. Это не исчерпывающе (плагины добавляют свои), но покрывает встроенные (Unity 2022.3+). Добавляй по нужде — не перегружай объект (экономь производительность)! Время на чтение: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (любой из курса).  
- Inspector: Выбери GameObject > Add Component > Поиск.  

**Предупреждение**: Некоторые компоненты конфликтуют (Rigidbody + CharacterController — нет). Добавляй по одному, тестируй в Play. Для кастом — создай скрипт. Если компонент "серый" — он по умолчанию (Transform). Тестируй на мобильном — не все работают (UI Canvas да, но оптимизируй).  

Готов экипировать объекты? По категориям, как по арсеналу! 📝

## 🔄 **Как Добавлять Компоненты? (Краткий Гайд)**
1. Выбери GameObject в Hierarchy.  
2. Inspector > Add Component (внизу).  
3. Поиск (e.g., "Rigidbody") или категории (Physics, UI).  
4. Drag & Drop свойства (e.g., AudioClip в AudioSource).  
5. Тест: Play — компонент работает?  

**Совет**: [RequireComponent] в скрипте — авто-добавляет (e.g., Rigidbody для физики).  

*Ссылка на документацию*: [Add Component](https://docs.unity3d.com/Manual/UsingComponents.html).

## 🏗️ **Категория 1: Основные Компоненты (Core)**
Базовые — всегда есть или легко добавить.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Transform** | Позиция, поворот, масштаб (по умолчанию). | Position (Vector3), Rotation (Quaternion), Scale (Vector3). | Позиция героя на арене. | [Transform](https://docs.unity3d.com/ScriptReference/Transform.html) |
| **RectTransform** | UI-версия Transform (anchors). | Anchors (Min/Max), Pivot (Vector2), Anchored Position. | Позиция HUD текста. | [RectTransform](https://docs.unity3d.com/ScriptReference/RectTransform.html) |

## 🎨 **Категория 2: Рендеринг (Rendering)**
Для визуала — mesh и материалы.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Mesh Filter** | Хранит mesh (форму). | Mesh (asset). | Форма пули (Sphere). | [MeshFilter](https://docs.unity3d.com/ScriptReference/MeshFilter.html) |
| **Mesh Renderer** | Рендерит mesh с материалами. | Materials (array), Cast Shadows, Receive Shadows. | Отрисовка врага с текстурой. | [MeshRenderer](https://docs.unity3d.com/ScriptReference/MeshRenderer.html) |
| **Skinned Mesh Renderer** | Для анимированных моделей (скелет). | Root Bone, Bounds, Quality (Auto/Skin). | Анимированный герой. | [SkinnedMeshRenderer](https://docs.unity3d.com/ScriptReference/SkinnedMeshRenderer.html) |
| **Line Renderer** | Рисует линии (траектории). | Positions (array), Width Curve, Material. | Трассеры пуль. | [LineRenderer](https://docs.unity3d.com/ScriptReference/LineRenderer.html) |
| **Trail Renderer** | Хвосты за объектом. | Time (длина), Width Curve, Autodestruct. | Хвост от летающей пули. | [TrailRenderer](https://docs.unity3d.com/ScriptReference/TrailRenderer.html) |
| **Particle System** | Частицы (огонь, дым). | Emission (rate), Shape (Sphere), Renderer (Material). | Взрывы на арене. | [ParticleSystem](https://docs.unity3d.com/ScriptReference/ParticleSystem.html) |
| **Projector** | Проекция текстуры (тени). | Material, Orthographic, Far Clip Plane. | Тени от врагов. | [Projector](https://docs.unity3d.com/ScriptReference/Projector.html) |

## 🏎️ **Категория 3: Физика (Physics)**
Для столкновений и движения.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Rigidbody** | Физическое тело (гравитация, силы). | Mass, Drag, Use Gravity, Is Kinematic. | Движение пули. | [Rigidbody](https://docs.unity3d.com/ScriptReference/Rigidbody.html) |
| **Rigidbody 2D** | 2D физика. | Mass, Drag, Gravity Scale. | Если 2D-версия шутера. | [Rigidbody2D](https://docs.unity3d.com/ScriptReference/Rigidbody2D.html) |
| **Box Collider** | Кубический коллайдер. | Center, Size, Material (Physics Material). | Стены арены. | [BoxCollider](https://docs.unity3d.com/ScriptReference/BoxCollider.html) |
| **Box Collider 2D** | 2D коробка. | Offset, Size. | 2D препятствия. | [BoxCollider2D](https://docs.unity3d.com/ScriptReference/BoxCollider2D.html) |
| **Sphere Collider** | Сферический. | Center, Radius, Is Trigger. | Пули. | [SphereCollider](https://docs.unity3d.com/ScriptReference/SphereCollider.html) |
| **Sphere Collider 2D** | 2D сфера. | Offset, Radius. | Круглые враги. | [CircleCollider2D](https://docs.unity3d.com/ScriptReference/CircleCollider2D.html) |
| **Capsule Collider** | Капсула. | Center, Radius, Height, Direction. | Герой (без углов). | [CapsuleCollider](https://docs.unity3d.com/ScriptReference/CapsuleCollider.html) |
| **Capsule Collider 2D** | 2D капсула. | Offset, Size, Direction. | 2D персонаж. | [CapsuleCollider2D](https://docs.unity3d.com/ScriptReference/CapsuleCollider2D.html) |
| **Mesh Collider** | По mesh. | Convex (упрощённый), Cooking Options. | Сложные модели. | [MeshCollider](https://docs.unity3d.com/ScriptReference/MeshCollider.html) |
| **Mesh Collider 2D** | 2D по mesh. | Offset, Cooking Options. | 2D формы. | [PolygonCollider2D](https://docs.unity3d.com/ScriptReference/PolygonCollider2D.html) |
| **Wheel Collider** | Для колёс. | Suspension Distance, Spring, Forward Friction. | Если добавить транспорт. | [WheelCollider](https://docs.unity3d.com/ScriptReference/WheelCollider.html) |
| **Character Controller** | Управление персонажем (кастом физика). | Center, Radius, Height, Slope Limit. | Герой без Rigidbody. | [CharacterController](https://docs.unity3d.com/ScriptReference/CharacterController.html) |
| **Constant Force** | Постоянная сила. | Force, Relative Force, Torque. | Ветер на арене. | [ConstantForce](https://docs.unity3d.com/ScriptReference/ConstantForce.html) |
| **Joint** (Hinge, Fixed, Spring, etc.) | Соединения. | Connected Body, Axis, Break Force. | Двери/механизмы. | [Joints](https://docs.unity3d.com/Manual/Joints.html) |

## 🔊 **Категория 4: Аудио (Audio)**
Для звуков.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Audio Source** | Источник звука. | AudioClip, Volume, Pitch, Spatial Blend. | Выстрел из бластера. | [AudioSource](https://docs.unity3d.com/ScriptReference/AudioSource.html) |
| **Audio Listener** | Слушатель (по умолчанию на камере). | — | Уши игрока. | [AudioListener](https://docs.unity3d.com/ScriptReference/AudioListener.html) |
| **Audio Reverb Zone** | Эхо-зона. | Room, RoomHF, Reflections. | Эхо в помещении арены. | [AudioReverbZone](https://docs.unity3d.com/ScriptReference/AudioReverbZone.html) |
| **Audio Reverb Filter** | Фильтр эха на источнике. | Reverb Preset, Room. | Голос в босс-фазе. | [AudioReverbFilter](https://docs.unity3d.com/ScriptReference/AudioReverbFilter.html) |
| **Audio Distortion Filter** | Искажение. | Distortion Level. | Взрывной эффект. | [AudioDistortionFilter](https://docs.unity3d.com/ScriptReference/AudioDistortionFilter.html) |
| **Audio Echo Filter** | Эхо. | Delay, Decay Ratio. | Эхо выстрелов. | [AudioEchoFilter](https://docs.unity3d.com/ScriptReference/AudioEchoFilter.html) |
| **Audio Low Pass Filter** | Низкие частоты. | Cutoff Frequency. | Приглушение вдали. | [AudioLowPassFilter](https://docs.unity3d.com/ScriptReference/AudioLowPassFilter.html) |
| **Audio High Pass Filter** | Высокие частоты. | Cutoff Frequency. | Фильтр шума. | [AudioHighPassFilter](https://docs.unity3d.com/ScriptReference/AudioHighPassFilter.html) |

## 🖥️ **Категория 5: UI (User Interface)**
Для интерфейсов.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Canvas** | Полотно UI. | Render Mode (Overlay), Scaler (Scale With Screen). | HUD с очками. | [Canvas](https://docs.unity3d.com/ScriptReference/Canvas.html) |
| **Canvas Scaler** | Адаптация размера. | UI Scale Mode, Reference Resolution. | Адаптивный HUD. | [CanvasScaler](https://docs.unity3d.com/ScriptReference/CanvasScaler.html) |
| **Canvas Group** | Группа для fade. | Alpha, Interactable, Blocks Raycasts. | Fade меню. | [CanvasGroup](https://docs.unity3d.com/ScriptReference/CanvasGroup.html) |
| **Graphic Raycaster** | Raycast для UI. | — | Клик по кнопкам. | [GraphicRaycaster](https://docs.unity3d.com/ScriptReference/GraphicRaycaster.html) |
| **Event System** | Обработка событий. | — | Навыход UI. | [EventSystem](https://docs.unity3d.com/ScriptReference/EventSystem.EventSystem.html) |
| **Image** | Изображение. | Source Image, Color, Raycast Target. | Иконка ammo. | [Image](https://docs.unity3d.com/ScriptReference/UI.Image.html) |
| **Raw Image** | Изображение без UV. | Texture, Color. | RenderTexture HUD. | [RawImage](https://docs.unity3d.com/ScriptReference/UI.RawImage.html) |
| **Text** | Текст (legacy). | Text, Font, Color. | Старый HUD. | [Text](https://docs.unity3d.com/ScriptReference/UI.Text.html) |
| **TextMeshPro - Text (UI)** | Продвинутый текст. | Text, Font Asset, Auto Size. | Score с тенями. | [TextMeshProUGUI](https://docs.unity3d.com/Packages/com.unity.textmeshpro@latest/index.html?subfolder=/manual/index.html) |
| **Button** | Кнопка. | OnClick (events), Transition (Color/Scale). | Рестарт. | [Button](https://docs.unity3d.com/ScriptReference/UI.Button.html) |
| **Toggle** | Переключатель. | Is On, Group, OnValueChanged. | Музыка on/off. | [Toggle](https://docs.unity3d.com/ScriptReference/UI.Toggle.html) |
| **Slider** | Ползунок. | Value, Min/Max, OnValueChanged. | Здоровье бар. | [Slider](https://docs.unity3d.com/ScriptReference/UI.Slider.html) |
| **Scrollbar** | Скроллбар. | Handle Rect, Direction. | Меню с опциями. | [Scrollbar](https://docs.unity3d.com/ScriptReference/UI.Scrollbar.html) |
| **Scroll View** | Скролл-контейнер. | Content, Viewport, Movement Type. | Список ачивок. | [ScrollRect](https://docs.unity3d.com/ScriptReference/UI.ScrollRect.html) |
| **Dropdown** | Выпадающий список. | Options, Value, OnValueChanged. | Выбор оружия. | [Dropdown](https://docs.unity3d.com/ScriptReference/UI.Dropdown.html) |
| **Input Field** | Поле ввода. | Text Component, Content Type (Number). | Имя игрока. | [InputField](https://docs.unity3d.com/ScriptReference/UI.InputField.html) |

## 🕺 **Категория 6: Анимация (Animation)**
Для движения.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Animation** | Legacy анимация. | Animations (array), Play Automatically. | Старые клипы. | [Animation](https://docs.unity3d.com/ScriptReference/Animation.html) |
| **Animator** | Современный контроллер. | Runtime Animator Controller, Apply Root Motion. | Состояния idle/shoot. | [Animator](https://docs.unity3d.com/ScriptReference/Animator.html) |
| **Animation Rigging** | IK (inverse kinematics). | Constraints (TwoBoneIK). | Руки героя к цели. | [Animation Rigging](https://docs.unity3d.com/Packages/com.unity.animation.rigging@latest) |

## 🧭 **Категория 7: Навигация (Navigation)**
Для AI пути.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **NavMesh Agent** | Агент на NavMesh. | Speed, Angular Speed, Stopping Distance. | Враги идут к герою. | [NavMeshAgent](https://docs.unity3d.com/ScriptReference/AI.NavMeshAgent.html) |
| **NavMesh Obstacle** | Препятствие. | Carve (динамическое), Shape (Box). | Динамические стены. | [NavMeshObstacle](https://docs.unity3d.com/ScriptReference/AI.NavMeshObstacle.html) |
| **NavMesh Surface** | Поверхность для NavMesh. | Agent Type, Collect Objects. | Bake NavMesh арены. | [NavMeshSurface](https://docs.unity3d.com/Packages/com.unity.ai.navigation@latest) |

## 🌪️ **Категория 8: Эффекты (Effects)**
Для частиц и ветра.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Wind Zone** | Ветер. | Mode (Radial), Wind Main, Turbulence. | Ветер толкает пули. | [WindZone](https://docs.unity3d.com/ScriptReference/WindZone.html) |
| **LOD Group** | Уровни детализации. | LOD Levels (array), Fade Mode. | Дальний враг — low poly. | [LODGroup](https://docs.unity3d.com/ScriptReference/LODGroup.html) |
| **Cloth** | Ткань. | Cloth (solver), Constraints. | Флаг на арене. | [Cloth](https://docs.unity3d.com/ScriptReference/Cloth.html) |
| **Terrain** | Ландшафт. | Terrain Data, Heightmap, Textures. | Процедурная арена. | [Terrain](https://docs.unity3d.com/ScriptReference/Terrain.html) |

## 📱 **Категория 9: Другие (Miscellaneous)**
Разное.

| Компонент | Описание | Ключевые Свойства | Пример в Шутере | Ссылка |
|-----------|----------|-------------------|-----------------|--------|
| **Camera** | Камера. | Field of View, Clear Flags, Culling Mask. | Вид сверху. | [Camera](https://docs.unity3d.com/ScriptReference/Camera.html) |
| **Flare Layer** | Линзовые блики. | Flare Strength, Flare Fade Speed. | Блики от взрывов. | [FlareLayer](https://docs.unity3d.com/ScriptReference/FlareLayer.html) |
| **Light** | Источник света. | Type (Directional), Intensity, Color. | Свет от дула. | [Light](https://docs.unity3d.com/ScriptReference/Light.html) |
| **Light Probe Group** | Проба света. | Probe Positions (array). | Динамическое освещение. | [LightProbeGroup](https://docs.unity3d.com/ScriptReference/LightProbeGroup.html) |
| **Occlusion Area** | Зона окклюзии. | Center, Size. | Оптимизация видимости. | [OcclusionArea](https://docs.unity3d.com/ScriptReference/OcclusionArea.html) |
| **Occlusion Portal** | Портал окклюзии. | Open (bool), Tracking. | Двери арены. | [OcclusionPortal](https://docs.unity3d.com/ScriptReference/OcclusionPortal.html) |
| **Reflection Probe** | Отражения. | Type (Baked), Importance. | Отражения в лужах. | [ReflectionProbe](https://docs.unity3d.com/ScriptReference/ReflectionProbe.html) |
| **Skybox / Cubemap** | Небо. | Custom Cubemap, Blend. | Фон арены. | [Skybox](https://docs.unity3d.com/Manual/Skybox.html) |
| **LOD Group** | Детализация. | LOD 0–4 (array), Fade. | Дальний босс low-poly. | [LODGroup](https://docs.unity3d.com/ScriptReference/LODGroup.html) |

## 💡 **Продвинутые Фишки Компонентов**
- **RequireComponent**: В скрипте — авто-добавляет (e.g., [RequireComponent(typeof(Rigidbody))] ).  
- **ExecuteInEditMode**: Компонент работает в редакторе.  
- **HideInInspector**: Скрыть поле от Inspector.  

**Эксперимент**: Добавь Rigidbody + Box Collider к кубу — толкни в Play (AddForce в скрипте).

*Ссылка на полный список*: [All Components](https://docs.unity3d.com/Manual/Components.html).

## Заключение: Компоненты — Твой Арсенал! 🎯
100+ компонентов — инструменты для экипировки GameObject: от Transform для позиции до Particle для эффектов. В шутере комбинируй (Rigidbody + Collider для пуль) — и битва реалистична. Освой — и строй миры!  

**Что Далее?**  
- Вернись к урокам — добавь компоненты.  
- Вопросы: [Unity Learn: Components](https://learn.unity.com/tutorial/components).  

Ты экипировал объекты — теперь играй! Продолжай, ассемблер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*