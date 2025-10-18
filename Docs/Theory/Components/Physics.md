# 🌪️ Физика и Коллайдеры: Законы Природы в Unity

Привет, физик-симулятор! 🌪️ Эта глава теории — твой полный гид по **физике и коллайдерам в Unity**: от Rigidbody (добавляет гравитацию и импульс) до коллайдеров (обнаружение касаний без визуала). Мы разберём, как они работают вместе для реалистичных столкновений, падений и взрывов в твоём топ-даун шутере (пули летят, враги отлетают). Включая свойства, методы, код-примеры и советы. Это ключ к динамике — без физики игра статична! Время на чтение: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (из курса, с ареной и героем).  
- Базовый скрипт (например, PlayerMovement).  

**Предупреждение**: Физика жрёт CPU — не добавляй Rigidbody везде. Используй FixedUpdate() для обновлений физики, чтобы избежать дёрганий. Если объекты "проходят" сквозь — увеличь Fixed Timestep в Project Settings > Time.  

Готов симулировать хаос? По разделам, как по законам Ньютона! 📝

## 🎯 **Что Такое Физика в Unity и Зачем Она Нужна?**
Unity использует встроенный **физический движок PhysX** (от NVIDIA) для симуляции реального мира: гравитация, столкновения, силы.  

- **Для чего?**  
  - Движение с инерцией: Пули отлетают, герой скользит после толчка.  
  - Реализм: Враги падают от выстрелов, арена реагирует на взрывы.  
  - Оптимизация: Не для всего — используй для динамики, статичные объекты (стены) без физики.  

**Основные Компоненты Физики**:  
- **Rigidbody**: Добавляет массу, скорость, силы.  
- **Collider**: "Невидимая оболочка" для касаний (Box, Sphere и т.д.).  
- **Joint**: Соединения (hinge, fixed) для механизмов.  

В твоём шутере: Rigidbody на пуле + Collider на враге = урон при попадании. Без них — объекты "призраки".  

**Как Включить?**: Add Component > Physics > Rigidbody/Collider.  

*Ссылка на официальную документацию*: [Unity Physics Overview](https://docs.unity3d.com/Manual/Physics.html).

## 🔄 **Как Работает Физика? (Rigidbody + Collider)**
Физика обновляется в **FixedUpdate()** (50 раз/сек по умолчанию), не в Update().  

- **Rigidbody**: "Тело" — даёт массу (Mass), гравитацию (Use Gravity), замедление (Drag). Без него Collider — статичный (не двигается).  
- **Collider**: Форма для обнаружения. Trigger — для событий без толчка (OnTriggerEnter), обычный — с отскоком (OnCollisionEnter).  
- **Взаимодействие**: Rigidbody + Collider = симуляция (столкновение вычисляет импульс).  

**Распространённые Ошибки**:  
- Нет Rigidbody — объект не реагирует на силы.  
- Layer Collision Matrix: В Edit > Project Settings > Physics — отключи столкновения между пулями.  
- Прохождение сквозь: Увеличь Solver Iterations (Physics Settings).  

**Пример в Шутере**: Пуля (Rigidbody + Sphere Collider Trigger) касается врага (Collider) — OnTriggerEnter снижает HP.

## ⚙️ **Свойства Rigidbody — Масса, Скорость и Силы**
Rigidbody — "сердце" динамики. Добавь к объекту для физики.  

| Свойство | Описание | Пример в Шутере | Значение по Умолчанию |
|----------|----------|-----------------|-----------------------|
| **Mass** (float) | Масса (в кг). Влияет на импульс. | Пуля: 0.1 (лёгкая, быстро летит). | 1 |
| **Drag** (float) | Воздушное сопротивление (замедление). | Враг: 0.5 (скользит после толчка). | 0 |
| **Angular Drag** (float) | Сопротивление вращению. | Босс: 5 (быстро останавливается). | 0.05 |
| **Use Gravity** (bool) | Гравитация. | Герой: On (не улетает). Пуля: Off. | On |
| **Is Kinematic** (bool) | Игнорирует физику (двигается скриптом). | Камера: On (стабильна). | Off |
| **Velocity** (Vector3) | Текущая скорость. | Пуля: `rb.velocity = direction * 20;`. | (0,0,0) |
| **Angular Velocity** (Vector3) | Скорость вращения. | Враг: `rb.angularVelocity = Vector3.up * 360;`. | (0,0,0) |

**Код для Настройки**:
```csharp
Rigidbody rb = GetComponent<Rigidbody>();
rb.mass = 0.5f;  // Лёгкий враг
rb.drag = 1f;    // Быстро тормозит
rb.useGravity = false;  // Летает
```

*Ссылка на документацию*: [Rigidbody Properties](https://docs.unity3d.com/ScriptReference/Rigidbody.html).

## 🔍 **Коллайдеры: Формы для Столкновений**
Коллайдеры — "формы" для обнаружения касаний. Выбери по объекту (Box для стен, Sphere для пуль).  

| Тип Коллайдера | Описание | Пример в Шутере | Свойства |
|----------------|----------|-----------------|----------|
| **Box Collider** | Кубическая форма. | Стены арены: Простой, дешёвый. | Center, Size (Vector3). |
| **Sphere Collider** | Сфера. | Пули: Круглое касание. | Center, Radius (float). |
| **Capsule Collider** | Капсула (цилиндр с полусферами). | Герой: Для персонажей. | Center, Radius, Height, Direction. |
| **Mesh Collider** | По mesh модели. | Сложные враги: Точная форма. | Convex (упрощённый для динамики). |
| **Wheel Collider** | Для колёс. | Не для шутера. | Suspension, Friction. |

**Общие Свойства**: Is Trigger (событие без толчка), Material (физический материал для трения).  

**Код для Добавления/Настройки**:
```csharp
// Добавь коллайдер программно
gameObject.AddComponent<SphereCollider>();
SphereCollider col = GetComponent<SphereCollider>();
col.radius = 0.5f;
col.isTrigger = true;  // Для урона без отскока
```

*Ссылка на документацию*: [Collider Types](https://docs.unity3d.com/Manual/CollidersOverview.html).

## 💻 **Методы и События Физики: OnCollision и Силы**
Физика реагирует событиями — используй в скриптах.  

### **События Столкновений**
- **OnCollisionEnter(Collision collision)**: Толчок (не Trigger).  
- **OnCollisionStay/Exit**: Во время/окончание.  
- **OnTriggerEnter(Collider other)**: Прохождение (Trigger=On).  

**Пример в Шутере: Урон от Пули**:
```csharp
public class BulletCollision : MonoBehaviour
{
    public int damage = 20;

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Enemy"))
        {
            EnemyHealth health = other.GetComponent<EnemyHealth>();
            if (health != null) health.TakeDamage(damage);

            // Отталкивание
            Rigidbody enemyRb = other.GetComponent<Rigidbody>();
            if (enemyRb != null)
            {
                Vector3 force = (other.transform.position - transform.position).normalized * 10f;
                enemyRb.AddForce(force, ForceMode.Impulse);
            }

            Destroy(gameObject);  // Уничтожь пулю
        }
    }
}
```
*Применение*: Прикрепи к пуле — враг отлетает и теряет HP.

### **Силы и Импульсы (Методы Rigidbody)**
- **AddForce(Vector3 force, ForceMode mode)**: Толкает.  
  Режимы: Force (постоянно), Impulse (мгновенно), VelocityChange (изменение скорости).  
  ```csharp
  rb.AddForce(Vector3.up * 10f, ForceMode.Impulse);  // Подброс
  ```
- **AddTorque(Vector3 torque)**: Вращение.  
  ```csharp
  rb.AddTorque(Vector3.right * 500f);  // Кувырок
  ```
- **AddExplosionForce(float explosionForce, Vector3 explosionPosition)**: Взрыв.  
  ```csharp
  rb.AddExplosionForce(1000f, transform.position, 5f);  // Взрыв от центра
  ```

**Пример Взрыва в Шутере**:
```csharp
public void ExplosionEffect(Vector3 center, float radius, float power)
{
    Collider[] hitColliders = Physics.OverlapSphere(center, radius);
    foreach (Collider hit in hitColliders)
    {
        Rigidbody rb = hit.GetComponent<Rigidbody>();
        if (rb != null)
        {
            rb.AddExplosionForce(power, center, radius);
        }
    }
}
```
*Применение*: Вызови при смерти босса — враги разлетаются!

*Ссылка на документацию*: [Rigidbody Forces](https://docs.unity3d.com/ScriptReference/Rigidbody.AddForce.html).

## 🎮 **Практические Примеры Физики в Твоём Шутере**
Готовый код — копируй в скрипты.

#### **Пример 1: Пуля с Физикой и Уроном**
```csharp
public class BulletPhysics : MonoBehaviour
{
    public float speed = 20f;
    public int damage = 25;
    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.velocity = transform.forward * speed;  // Полёт
    }

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Enemy"))
        {
            other.GetComponent<EnemyHealth>().TakeDamage(damage);
            rb.AddExplosionForce(500f, other.transform.position, 2f);  // Толчки врагов
            Destroy(gameObject);
        }
    }
}
```
*Применение*: Instantiate в PlayerMovement — пули с отскоком.

#### **Пример 2: Враг с Физикой (Отлёт от Удара)**
```csharp
public class EnemyPhysics : MonoBehaviour
{
    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.mass = 2f;  // Тяжёлый, трудно толкнуть
    }

    public void Knockback(Vector3 direction, float force)
    {
        rb.AddForce(direction * force, ForceMode.Impulse);
    }
}
```
*Применение*: В BulletDamage вызови Knockback — враг отлетает.

#### **Пример 3: Гравитация для Предметов (Бонусы Падают)**
```csharp
public class FallingItem : MonoBehaviour
{
    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.useGravity = true;
        rb.drag = 0.8f;  // Замедление при падении
    }

    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            rb.useGravity = false;  // Остановка на земле
        }
    }
}
```
*Применение*: Спавнь бонусы сверху — они падают на арену.

## 💡 **Продвинутые Фишки Физики**
- **Layers и Collision Matrix**: Edit > Project Settings > Physics — отключи пули с пулями.  
- **Continuous Collision**: В Rigidbody Collision Detection = Continuous Dynamic — для быстрых объектов (пули не проходят).  
- **Query Triggers**: В Physics.RaycastAll — игнорируй триггеры.  

**Эксперимент**: Добавь Box Collider к арене (статичный, без Rigidbody). Стреляй в стену — пуля отскочит!

*Ссылка на продвинутые примеры*: [Advanced Physics](https://learn.unity.com/tutorial/advanced-physics).

## Заключение: Физика — Душа Динамики 🌪️
Rigidbody + Collider — это симуляция жизни: толчки, падения, касания. В твоём шутере они делают пули смертоносными, а битву — хаотичной. Освой — и твои игры оживут!  

**Что Далее?**  
- Перейди к [UI и Интерфейс](./UI.md) — визуал поверх физики.  
- Вопросы: [Unity Learn: Physics and Colliders](https://learn.unity.com/tutorial/physics-and-colliders).  

Ты симулировал гравитацию — теперь лети! Продолжай, ньютон. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*