# 🏗️ Продвинутые Фишки GameObjects: Создание и Управление в Коде

Привет, кодовый конструктор! 🏗️ Эта глава продвинутой теории — твой полный гид по **продвинутым фишкам GameObjects в Unity**: от создания объектов в коде (new GameObject + AddComponent) до Prefab Variants (кастом-версии шаблонов) и Dynamic Creation (Instantiate для runtime, как пули). Мы углубим твой топ-даун шутер: генерируй врагов процедурно, вариации prefab для типов ботов, динамический спавн с пулингом. Это шаг от меню Create к автоматизации — код строит мир! Время на чтение: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" (из Урока 8+).  
- Скрипт (например, Spawner.cs).  

**Предупреждение**: new GameObject() — runtime, не сохраняется в сцене (для временных, как пули). Prefab Variants — только Unity 2018+, для старых используй base prefab. Instantiate без пула — memory leaks (спавнь 1000+ — краш). Тестируй в Profiler (Memory > Allocated). Если объекты "розовеют" — сломанные ссылки (Reimport).  

Готов кодить миры? По фишкам, как по конструктору! 📝

## 🎯 **Что Такое Продвинутые Фишки GameObjects и Зачем Они?**
GameObjects — базовые "ячейки", но продвинутые техники позволяют генерировать их динамически: в коде (без меню), вариации prefab (кастом от базового) и runtime создание (Instantiate для пуль/врагов).  

- **Для чего Создание в Коде?**  
  - Процедурно: Генерируй 10 стен случайных размеров.  
  - Временные: Пули — new + Destroy через 5 сек.  

- **Для чего Prefab Variants?**  
  - Кастом: Базовый Enemy prefab — variant "FastEnemy" с +speed.  

- **Для чего Dynamic Creation?**  
  - Спавн: Instantiate(prefab) в Update — волны врагов.  
  - Пул: Переиспользуй (экономь Instantiate).  

В твоём шутере: Код создаст случайные препятствия на арене, variant prefab для типов врагов, dynamic пули без лагов. Без фишек — статичная сцена, с ними — живой хаос.  

**Как Начать?**: new GameObject("Name"); prefabVariant = PrefabUtility.InstantiatePrefab(basePrefab); Instantiate(prefab, pos, rot);.  

*Ссылка на официальную документацию*: [GameObject Creation](https://docs.unity3d.com/ScriptReference/GameObject.html) и [Prefab Variants](https://docs.unity3d.com/Manual/PrefabVariants.html).

## 🔄 **Фишка 1: Создание GameObjects в Коде (new + AddComponent)**
Создавай объекты runtime — без Hierarchy.

- **Процесс**: new GameObject() — пустой, AddComponent() — экипируй.  
- **Преимущества**: Процедурные уровни, эффекты.  

**Распространённые Ошибки**:  
- Нет Transform — добавь вручную.  
- Memory: Destroy() после использования.  

**Код: Создание Пули в Коде** (расширь Shoot()):
```csharp
using UnityEngine;

public class CodeCreationExample : MonoBehaviour
{
    public Material bulletMaterial;  // Перетащи

    void CreateBulletInCode(Vector3 startPos, Vector3 direction, float speed = 20f)
    {
        // Новый GO
        GameObject bullet = new GameObject("DynamicBullet");

        // Компоненты
        bullet.transform.position = startPos;
        bullet.transform.rotation = Quaternion.LookRotation(direction);

        // Mesh + Renderer
        MeshFilter meshFilter = bullet.AddComponent<MeshFilter>();
        meshFilter.mesh = Resources.GetBuiltinResource<Mesh>("Sphere.fbx");  // Встроенная сфера

        MeshRenderer renderer = bullet.AddComponent<MeshRenderer>();
        renderer.material = bulletMaterial;

        // Физика
        Rigidbody rb = bullet.AddComponent<Rigidbody>();
        rb.useGravity = false;
        rb.velocity = direction * speed;

        SphereCollider col = bullet.AddComponent<SphereCollider>();
        col.isTrigger = true;

        // Скрипт
        BulletDamage damageScript = bullet.AddComponent<BulletDamage>();
        damageScript.damage = 20;

        // Destroy через 5 сек
        Destroy(bullet, 5f);

        Debug.Log("Dynamic bullet created!");
    }

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector3 dir = (Camera.main.ScreenToWorldPoint(Input.mousePosition) - transform.position).normalized;
            CreateBulletInCode(transform.position, dir);
        }
    }
}
```
*Применение*: Пули генерируются на лету — procedural без prefab.

*Ссылка на документацию*: [new GameObject](https://docs.unity3d.com/ScriptReference/GameObject.GameObject.html).

## 🛠️ **Фишка 2: Prefab Variants — Кастом Версии Шаблонов**
Variants — "ветви" prefab: базовый + переопределения (override свойства).

- **Процесс**: Открой prefab > Asset > Create > Prefab Variant.  
- **Преимущества**: Общий код, разные stats (Enemy base + Fast variant).  

**Распространённые Ошибки**:  
- Override не применяется — кликни Apply в Inspector.  
- Цепочка variants — лимит глубины (5–10).  

**Код: Использование Variant в Спавне** (расширь Spawner):
```csharp
using UnityEngine;

public class VariantSpawner : MonoBehaviour
{
    public GameObject baseEnemyPrefab;  // Базовый prefab
    public GameObject fastVariant;      // Variant от base

    void SpawnVariant(bool isFast)
    {
        GameObject prefabToUse = isFast ? fastVariant : baseEnemyPrefab;

        GameObject enemy = Instantiate(prefabToUse, RandomPos(), Quaternion.identity);
        
        // Override в runtime (если нужно)
        EnemyAI ai = enemy.GetComponent<EnemyAI>();
        ai.speed = isFast ? 5f : 2f;  // Переопределение

        // Variant сохранит базовые компоненты (Collider, etc.)
    }

    Vector3 RandomPos()
    {
        return new Vector3(Random.Range(-10,10), 0, Random.Range(-10,10));
    }
}
```
*Применение*: fastVariant — +speed от base, спавнь случайно.

*Ссылка на документацию*: [Prefab Variants](https://docs.unity3d.com/Manual/PrefabVariants.html).

## 🚀 **Фишка 3: Dynamic Creation — Instantiate для Runtime**
Instantiate — "клон" prefab в коде, для динамики.

- **Процесс**: Instantiate(prefab, pos, rot, parent);  
- **Преимущества**: Спавн по событию (волна врагов).  

**Распространённые Ошибки**:  
- Memory: Пул (Object Pool) для частого (пули).  
- Quaternion.identity — дефолт поворот.  

**Код: Dynamic Пул Пуль** (расширь Shoot()):
```csharp
using UnityEngine;
using System.Collections.Generic;

public class DynamicPool : MonoBehaviour
{
    public GameObject bulletPrefab;
    private Queue<GameObject> pool = new Queue<GameObject>();
    public int poolSize = 50;

    void Start()
    {
        for (int i = 0; i < poolSize; i++)
        {
            GameObject bullet = Instantiate(bulletPrefab);
            bullet.SetActive(false);
            pool.Enqueue(bullet);
        }
    }

    public GameObject GetPooledBullet(Vector3 pos, Quaternion rot)
    {
        GameObject bullet;
        if (pool.Count > 0)
        {
            bullet = pool.Dequeue();
        }
        else
        {
            bullet = Instantiate(bulletPrefab, pos, rot);  // Fallback
        }

        bullet.transform.SetPositionAndRotation(pos, rot);
        bullet.SetActive(true);

        // Авто-возврат в пул через 3 сек
        StartCoroutine(ReturnToPool(bullet, 3f));

        return bullet;
    }

    IEnumerator ReturnToPool(GameObject obj, float delay)
    {
        yield return new WaitForSeconds(delay);
        obj.SetActive(false);
        pool.Enqueue(obj);
    }

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Vector3 dir = (Camera.main.ScreenToWorldPoint(Input.mousePosition) - transform.position).normalized;
            GetPooledBullet(transform.position, Quaternion.LookRotation(dir));
        }
    }
}
```
*Применение*: 1000+ пуль без лагов — пул возвращает.

*Ссылка на документацию*: [Instantiate](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html).

## 💡 **Продвинутые Фишки GameObjects**
- **Prefab Utility**: PrefabUtility.SaveAsPrefabAsset — сохрани в коде.  
- **Addressables**: Асинхронный спавн (для больших проектов).  
- **Batch Creation**: for-loop + Instantiate — генерируй армию.  

**Эксперимент**: new GameObject("Test") + AddComponent<Renderer>() — объект в runtime.

*Ссылка на продвинутые примеры*: [Dynamic GameObjects](https://learn.unity.com/tutorial/dynamic-gameobjects).

## Заключение: GameObjects в Твоих Руках! 🏗️
Создание в коде — procedural, variants — кастом, dynamic — живой спавн. В шутере это случайные волны + пули без тормозов. Освой — и сцены генерируются!  

**Что Далее?**  
- Вернись к урокам — используй в спавне.  
- Вопросы: [Unity Learn: Prefabs Advanced](https://learn.unity.com/tutorial/prefabs-advanced).  

Ты кодил обджекты — теперь миры твои! Продолжай, генератор. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
