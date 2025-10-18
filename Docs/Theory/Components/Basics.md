# 🏛️ Основные Компоненты: Transform и Mesh (Фундаментальные Элементы Unity)

Привет, строитель миров! 🏛️ Эта глава теории — глубокий разбор **основных компонентов Unity**: сначала **Transform** (позиция, поворот, масштаб — основа всего движения и размещения), а потом **Mesh Filter и Mesh Renderer** (визуальная "мясо" объектов для рендеринга). Мы возьмём предоставленный тобой документ и расширим его: добавим объяснения, примеры кода, советы, ссылки на официальную документацию и практические применения в твоём топ-даун шутере. Это справочник для новичков — читай, копируй код, экспериментируй! Время на чтение: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (например, из курса).  
- Visual Studio для тестирования скриптов.  

**Предупреждение**: Transform и Mesh — базовые, но ошибки в них (например, нулевой Scale) могут сломать всю сцену. Всегда проверяй в Console и Scene View!  

Готов разобрать фундамент? Сначала Transform, потом Mesh. По разделам, как по кирпичикам! 📝

## 🎯 **Раздел 1: Transform (Позиция, Вращение и Масштаб) — Сердце Каждого Объекта**

Отличный вопрос! Объясню **Transform** в Unity так, чтобы было понятно новичку. Transform — это **главный компонент** любого объекта в Unity. Если представить игровой объект как человека, то Transform — это его **паспорт с данными о местоположении**. Без него объект "невидим" в мире — он просто файл в Project.

### 📍 **Три Основных Параметра Transform**
Transform определяет **где**, **как повернут** и **насколько велик** объект. Вот базовые свойства:

#### **1. Position (Позиция)**
```csharp
// Где находится объект в мире
transform.position = new Vector3(0, 0, 0);
```
- **X** — горизонталь (вправо/влево).  
- **Y** — вертикаль (вверх/вниз).  
- **Z** — глубина (вперёд/назад).  
- **Пример в шутере**: `(2, 1, -5)` — герой смещён на 2 вправо, 1 вверх и 5 назад от центра арены.  

*Ссылка на документацию*: [Transform.position](https://docs.unity3d.com/ScriptReference/Transform-position.html).

#### **2. Rotation (Поворот)**
```csharp
// Как повернут объект
transform.rotation = Quaternion.Euler(0, 90, 0);
```
- **X** — наклон вперёд/назад (pitch).  
- **Y** — поворот влево/вправо (yaw).  
- **Z** — крен вбок (roll).  
- **Пример в шутере**: `(0, 90, 0)` — герой повернут на 90° вправо, чтобы смотреть на врага. Quaternion — это "математический поворот", Euler Angles — удобные градусы в Inspector.  

*Ссылка на документацию*: [Transform.rotation](https://docs.unity3d.com/ScriptReference/Transform-rotation.html).

#### **3. Scale (Масштаб)**
```csharp
// Размер объекта
transform.localScale = new Vector3(2, 2, 2);
```
- **X** — ширина.  
- **Y** — высота.  
- **Z** — глубина.  
- **Пример в шутере**: `(2, 2, 2)` — пуля в 2 раза больше, чтобы лучше видно. Избегай нулевого Scale — объект исчезнет!  

*Ссылка на документацию*: [Transform.localScale](https://docs.unity3d.com/ScriptReference/Transform-localScale.html).

### 🎮 **Практические Примеры Transform в Твоём Шутере**
Вот код, готовый к копипасту — протестируй на герое или пуле.

#### **Пример 1: Движение Персонажа (WASD + Поворот к Мыши)**
```csharp
public class PlayerController : MonoBehaviour
{
    public float speed = 5f;

    void Update()
    {
        // Движение
        float horizontal = Input.GetAxis("Horizontal");  // A/D
        float vertical = Input.GetAxis("Vertical");      // W/S
        Vector3 movement = new Vector3(horizontal, 0f, vertical) * speed * Time.deltaTime;
        transform.Translate(movement, Space.World);  // Глобальное движение

        // Поворот к мыши
        Vector3 mousePos = Camera.main.ScreenToWorldPoint(Input.mousePosition);
        mousePos.y = transform.position.y;  // Остаёмся на плоскости
        Vector3 direction = mousePos - transform.position;
        if (direction != Vector3.zero)
        {
            float angle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg;
            transform.rotation = Quaternion.Euler(0f, angle, 0f);
        }
    }
}
```
*Применение*: Добавь к герою — он бегает и смотрит на курсор, как в твоём шутере.

#### **Пример 2: Изменение Размера (Power-Up Эффект)**
```csharp
public class PowerUpEffect : MonoBehaviour
{
    public void ActivatePowerUp()
    {
        // Увеличить героя в 1.5 раза на 5 секунд
        StartCoroutine(TemporaryScale());
    }

    IEnumerator TemporaryScale()
    {
        Vector3 originalScale = transform.localScale;
        transform.localScale *= 1.5f;

        yield return new WaitForSeconds(5f);

        transform.localScale = originalScale;  // Вернуть назад
    }
}
```
*Применение*: Вызови при сборе бонуса — герой "накачается"!

#### **Пример 3: Телепортация (Респавн После Смерти)**
```csharp
public class RespawnSystem : MonoBehaviour
{
    public Vector3 spawnPoint = new Vector3(0, 1, 0);

    public void Respawn()
    {
        // Телепорт + сброс поворота
        transform.SetPositionAndRotation(spawnPoint, Quaternion.identity);
        // Сброс скорости физики
        GetComponent<Rigidbody>().velocity = Vector3.zero;
    }
}
```
*Применение*: В UIHealthManager при health=0 — Respawn() героя.

### 🌍 **Local vs World Space: Локальное vs Глобальное**
- **World Position/Rotation/Scale**: Относительно сцены (центр мира (0,0,0)). Используй для абсолютного размещения.  
- **Local**: Относительно родителя. Идеально для детей (например, пуля local к дулу).  

**Пример**:
```
Родитель (арена) в (5, 0, 0)
Ребёнок (враг) localPosition (2, 0, 0)
Глобальная позиция ребёнка: (7, 0, 0)
```
Код: `Vector3 worldPos = parent.TransformPoint(child.localPosition);`

*Ссылка на документацию*: [TransformPoint](https://docs.unity3d.com/ScriptReference/Transform.TransformPoint.html).

### 🔧 **Часто Используемые Методы Transform**
Вот полный список ключевых методов с примерами — расширенный из твоего документа.

#### **Методы Движения и Позиционирования**
- **Translate(Vector3 displacement, Space relativeTo = Space.Self)**: Сдвиг.  
  ```csharp
  transform.Translate(Vector3.forward * 5 * Time.deltaTime, Space.World);  // Вперед в мировых координатах
  ```
- **SetPositionAndRotation(Vector3 position, Quaternion rotation)**: Установка сразу.  
  ```csharp
  transform.SetPositionAndRotation(new Vector3(0,0,0), Quaternion.Euler(0,180,0));
  ```

#### **Методы Поворота и Вращения**
- **Rotate(Vector3 eulers, Space relativeTo = Space.Self)**: Поворот.  
  ```csharp
  transform.Rotate(0, 90 * Time.deltaTime, 0, Space.World);  // Плавный yaw
  ```
- **RotateAround(Vector3 point, Vector3 axis, float angle)**: Вокруг точки.  
  ```csharp
  transform.RotateAround(Vector3.zero, Vector3.up, 360 * Time.deltaTime);  // Орбита вокруг центра
  ```
- **LookAt(Transform target)**: Смотреть на цель.  
  ```csharp
  transform.LookAt(enemy.transform);  // Герой смотрит на врага
  ```

#### **Методы Иерархии**
- **SetParent(Transform parent, bool worldPositionStays = true)**: Родитель.  
  ```csharp
  bullet.SetParent(gunTransform, true);  // Пуля child ружья, позиция сохраняется
  ```
- **GetChild(int index)**: Ребёнок.  
  ```csharp
  Transform child = transform.GetChild(0);  // Первый ребёнок
  ```
- **Find(string name)**: Поиск по имени.  
  ```csharp
  Transform gun = transform.Find("Gun");  // В иерархии героя
  ```

#### **Методы Направлений и Преобразований**
- **TransformDirection(Vector3 direction)**: Локальное в глобальное.  
  ```csharp
  Vector3 globalDir = transform.TransformDirection(Vector3.forward);  // "Вперёд" героя в мир
  ```
- **InverseTransformDirection(Vector3 direction)**: Обратно.  
  ```csharp
  Vector3 localDir = transform.InverseTransformDirection(worldDir);  // Мир в локальный
  ```
- **TransformPoint(Vector3 position)**: Точку локальную в глобальную.  
  ```csharp
  Vector3 spawnPos = transform.TransformPoint(0, 0, 2);  // 2 единицы вперёд от героя
  ```

### ❓ **Почему Transform Так Важен?**
1. **Без него объект не существует** в сцене — только в Project.  
2. **Все движения** (скрипты, анимации) через Transform.  
3. **Физика** (Rigidbody) опирается на него для расчётов.  
4. **Иерархия**: Родители влияют на детей (Scale родителя умножается).  

**Аналогия**: Transform — GPS + компас + линейка объекта. Без него — потерян в бесконечности!

*Ссылка на официальную документацию*: [Полный Гайд по Transform](https://docs.unity3d.com/Manual/class-Transform.html).

### 🎯 **Продвинутые Фишки Transform (Расширение Твоего Документа)**
Вот неочевидные трюки для твоего шутера — с кодом.

#### **1. Вращение с Ограничениями (Для Камеры или Оружия)**
```csharp
public class LimitedRotation : MonoBehaviour
{
    public float minX = -45f, maxX = 45f;

    void Update()
    {
        float mouseY = Input.GetAxis("Mouse Y") * 100 * Time.deltaTime;
        Vector3 euler = transform.eulerAngles;
        euler.x = Mathf.Clamp(euler.x > 180 ? euler.x - 360 : euler.x + mouseY, minX, maxX);
        transform.eulerAngles = euler;
    }
}
```
*Применение*: Оружие не крутится бесконечно.

#### **2. Пульсация Размера (Эффект Power-Up)**
```csharp
public class PulsatingScale : MonoBehaviour
{
    public float speed = 2f, minScale = 0.8f, maxScale = 1.2f;

    void Update()
    {
        float scale = Mathf.Lerp(minScale, maxScale, (Mathf.Sin(Time.time * speed) + 1) / 2);
        transform.localScale = Vector3.one * scale;
    }
}
```
*Применение*: Бонус мигает на арене.

#### **3. Сохранение/Восстановление (Для Чекпоинтов)**
```csharp
public class Checkpoint : MonoBehaviour
{
    private Vector3 savedPos; private Quaternion savedRot;

    public void SaveState() => (savedPos, savedRot) = (transform.position, transform.rotation);

    public void LoadState() => (transform.position, transform.rotation) = (savedPos, savedRot);
}
```
*Применение*: Сохрани позицию перед боссом.

#### **4. Дрожание При Уроне**
```csharp
public IEnumerator Shake(float duration, float magnitude)
{
    Vector3 originalPos = transform.localPosition;
    float elapsed = 0f;

    while (elapsed < duration)
    {
        transform.localPosition = originalPos + Random.insideUnitSphere * magnitude;
        elapsed += Time.deltaTime;
        yield return null;
    }

    transform.localPosition = originalPos;
}
```
*Применение*: Вызови при попадании пули — экран трясётся!

*Ссылка на продвинутые примеры*: [Transform в Шутерах](https://learn.unity.com/tutorial/transforms-and-parenting).

### **Практические Советы и Ошибки**
- **Time.deltaTime**: Всегда умножай на него для плавности (независимо от FPS).  
- **Кэшируй**: `private Transform myTrans = transform;` — быстрее доступа.  
- **Ошибки**: NullReference — проверь parent. Scale (0,0,0) — объект невидим.  

**Эксперимент**: Создай 3 куба, сделай цепочку parent-child. Измени Scale родителя — все вырастут!

## 📐 **Раздел 2: Mesh Filter и Mesh Renderer (Визуальная "Кожа" Объектов)**

Теперь перейдём к **Mesh Filter и Mesh Renderer** — паре компонентов, которые "одевают" Transform в видимую форму. Mesh Filter хранит "сетку" (геометрию), Renderer рисует её с материалами. Вместе они превращают абстрактный Transform в куб или сферу на экране.

### **Что Такое Mesh Filter и Зачем Он Нужен?**
Mesh Filter — "скелет" визуала: содержит **Mesh** (сетку вершин, треугольников). Без него Renderer не знает, что рисовать.  

- **Для чего?**  
  - Определяет форму: Куб, сфера, импортированная модель.  
  - Базовые примитивы: Unity создаёт их автоматически (3D Object > Cube).  
  - Импорт: FBX-модели приносят свой Mesh.  

**Пример в шутере**: Mesh Filter для пули — простая сфера, чтобы пуля была круглой.

*Ссылка на документацию*: [Mesh Filter Component](https://docs.unity3d.com/ScriptReference/MeshFilter.html).

### **Как Работает Mesh Filter?**
- **Mesh**: Массив вершин (positions), нормалей (для света), UV (для текстур).  
- **Автогенерация**: Примитивы (Cube) имеют встроенный Mesh.  
- **Редактирование**: В ProBuilder (Window > Package Manager > ProBuilder) — моделируй вручную.  

**Распространённые ошибки**:  
- Нет Mesh — объект невидим.  
- Неправильный импорт — вершины "перевёрнуты" (проверь Scale в FBX).  

### **Свойства Mesh Filter**
В Inspector: Только **Mesh** поле — перетащи ассет.  

Код:
```csharp
MeshFilter filter = GetComponent<MeshFilter>();
Mesh myMesh = filter.mesh;  // Доступ к сетке
myMesh.vertices = new Vector3[] { /* новые вершины */ };  // Редактируй динамически
```

### **Mesh Renderer: Рисование Сетки**
Renderer берёт Mesh от Filter и "красит" его материалами, освещением.  

- **Для чего?**  
  - Применяет материалы (текстуры, цвета).  
  - Взаимодействует со светом (shadows, reflections).  
  - Куллинг: Не рендерит невидимые части (backface culling).  

**Пример в шутере**: Renderer для врага — красный материал + свет от взрыва.

*Ссылка на документацию*: [Mesh Renderer Component](https://docs.unity3d.com/ScriptReference/MeshRenderer.html).

### **Свойства Mesh Renderer**
| Свойство | Описание | Пример в Шутере |
|----------|----------|-----------------|
| **Materials** | Массив материалов. | Красный для врага, зелёный для бонуса. |
| **Cast Shadows** | Тени от объекта. | On — враги отбрасывают тени. |
| **Receive Shadows** | Принимает тени. | Арена получает тени от пуль. |
| **Light Probes** | Глобальное освещение. | Для динамических объектов. |

Код:
```csharp
MeshRenderer renderer = GetComponent<MeshRenderer>();
renderer.material.color = Color.red;  // Изменить цвет
renderer.enabled = false;  // Скрыть объект
```

### **Практические Примеры Mesh Filter + Renderer**
#### **Пример 1: Динамическая Смена Mesh (Форма Пули)**
```csharp
public class DynamicMesh : MonoBehaviour
{
    public Mesh[] bulletMeshes;  // Массив форм (сфера, куб)

    void Start()
    {
        MeshFilter filter = GetComponent<MeshFilter>();
        filter.mesh = bulletMeshes[0];  // Первая форма
    }

    public void ChangeShape(int index)
    {
        GetComponent<MeshFilter>().mesh = bulletMeshes[index];
        GetComponent<MeshRenderer>().material.color = Color.cyan;  // Новый цвет
    }
}
```
*Применение*: Меняй пулю на лазер после апгрейда.

#### **Пример 2: Визуализация Урона (Красный Flash)**
```csharp
public class DamageFlash : MonoBehaviour
{
    public float flashDuration = 0.2f;
    private Renderer meshRenderer;
    private Color originalColor;

    void Start()
    {
        meshRenderer = GetComponent<MeshRenderer>();
        originalColor = meshRenderer.material.color;
    }

    public void FlashRed()
    {
        StartCoroutine(FlashCoroutine());
    }

    IEnumerator FlashCoroutine()
    {
        meshRenderer.material.color = Color.red;
        yield return new WaitForSeconds(flashDuration);
        meshRenderer.material.color = originalColor;
    }
}
```
*Применение*: Вызови при уроне — враг краснеет!

#### **Пример 3: Импорт и Применение Mesh**
```csharp
public class MeshImporter : MonoBehaviour
{
    public TextAsset meshData;  // JSON с вершинами (для процедурных)

    void Start()
    {
        MeshFilter filter = gameObject.AddComponent<MeshFilter>();
        MeshRenderer renderer = gameObject.AddComponent<MeshRenderer>();

        Mesh newMesh = new Mesh();
        // Парсинг данных в вершины (упрощённо)
        newMesh.vertices = new Vector3[] { new Vector3(0,0,0), new Vector3(1,0,0), new Vector3(0,1,0) };
        newMesh.triangles = new int[] { 0, 2, 1 };
        filter.mesh = newMesh;

        renderer.material = new Material(Shader.Find("Standard"));  // Базовый шейдер
    }
}
```
*Применение*: Создай процедурную арену из случайных meshes.

### **Local vs World в Mesh**
Mesh рендерится в локальном пространстве Transform, но виден глобально. Изменение parent Transform влияет на вид (например, поворот родителя крутит mesh).

**Ошибки**: Нет Renderer — mesh не рисуется. Неправильный материал — чёрный объект.

*Ссылка на документацию*: [Mesh Rendering](https://docs.unity3d.com/Manual/MeshRenderers.html).

### **Практические Советы для Mesh**
- **Оптимизация**: Используй LOD (Levels of Detail) для дальних объектов — простой mesh на расстоянии.  
- **Батчинг**: Группируй статичные meshes для скорости.  
- **Эксперимент**: Импортируй FBX-модель из Asset Store, замени mesh куба — увидишь разницу.  

**Эксперимент**: Создай куб, добавь Mesh Filter вручную (примитив Cube), Renderer с текстурой — объект оживёт!

## Заключение: Transform + Mesh — Основа Визуала и Динамики 🌌
Transform — "где и как", Mesh Filter/Renderer — "что видно". Вместе они строят твой шутер: Transform двигает пулю, Mesh делает её сферой с блеском. Освой — и моделируй миры!  

**Что Далее?**  
- Перейди к [Физика и Коллайдеры](./Physics.md) — столкновения для пуль.  
- Вопросы: [Unity Learn: Meshes and Rendering](https://learn.unity.com/tutorial/meshes-and-rendering).  

Ты разобрал фундамент — теперь строй империи! Продолжай, визионер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*