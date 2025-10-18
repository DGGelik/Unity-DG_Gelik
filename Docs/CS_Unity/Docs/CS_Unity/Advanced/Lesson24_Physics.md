
# 🌪️ Урок 24: Физика — Rigidbody и Силы

Привет, юный физик арены! 💻 Добро пожаловать на одиннадцатый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **физику** в Unity, используя **Rigidbody** и силы, чтобы добавить реалистичные взаимодействия в твой топ-даун шутер (*UnityTopDownShooterQuest*). Ты сделаешь пули физическими и добавишь отталкивание врагов! Готов управлять силами? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController` из [Урок 23: Сцены](../Advanced/Lesson23_Scenes.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя физическая лаборатория!  

**Предупреждение**: Физика требует правильной настройки **Rigidbody** и **Collider**. Сохраняй код (**Ctrl+S**) и префабы перед тестом, иначе физика не сработает! Если пули или враги не взаимодействуют, проверь настройки **Rigidbody** (Constraints, Is Kinematic). Врубай Play Mode и управляй силами!

Готов добавить физику? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Rigidbody и Силы?**
**Rigidbody** — компонент Unity для физических объектов, реагирующих на силы, гравитацию и столкновения.  
- **AddForce**: Применяет силу к объекту.  
- **Constraints**: Ограничивает движение или вращение.  
- **Is Kinematic**: Отключает физику, но сохраняет столкновения.  

В твоём шутере ты сделаешь пули физическими и добавишь отталкивание врагов.

*Ссылка на документацию*: [Rigidbody](https://docs.unity3d.com/ScriptReference/Rigidbody.html).

## 🔄 **Зачем это Нужно?**
Физика делает взаимодействия реалистичными и динамичными. В этом квесте ты:  
- Сделаешь пули физическими с `Rigidbody`.  
- Добавишь отталкивание врагов при попадании.  
- Усилишь ощущение боя!  

**Почему это круто?**  
- **Реализм**: Пули и враги реагируют физически.  
- **Динамика**: Отталкивание добавляет тактильность.  
- **Для шутера**: Физика делает бой эпичным!  

**Типичные ошибки новичков**:  
- Неправильные настройки **Rigidbody** (например, не заморожено вращение).  
- Слишком большая/маленькая сила в `AddForce`.  
- Отсутствие **Collider** на объектах.  

## ⚙️ **Квест: Добавь физику**

### Уровень 1: Физические пули  
1. **Обнови `BulletController`:**  
   - Замени `velocity` на `AddForce`:  
     ```csharp
     public void Shoot(Vector3 direction, float speed)
     {
         Rigidbody rb = GetComponent<Rigidbody>();
         rb.AddForce(direction * speed, ForceMode.Impulse);
         Debug.Log("Пуля запущена с силой!");
     }
     ```

2. **Обнови `PlayerController`:**  
   - Вызови `Shoot` из пула:  
     ```csharp
     bullet.GetComponent<BulletController>().Shoot(direction, bulletSpeed);
     ```

3. **Полный код `BulletController`:**  
   ```csharp
   using UnityEngine;

   public class BulletController : MonoBehaviour
   {
       [SerializeField] private float lifetime = 3.0f;

       void OnEnable()
       {
           Invoke(nameof(ReturnToPool), lifetime);
       }

       public void Shoot(Vector3 direction, float speed)
       {
           Rigidbody rb = GetComponent<Rigidbody>();
           rb.AddForce(direction * speed, ForceMode.Impulse);
           Debug.Log("Пуля запущена с силой!");
       }

       void OnCollisionEnter(Collision other)
       {
           if (other.gameObject.CompareTag("Enemy") || other.gameObject.CompareTag("Boss"))
           {
               other.gameObject.GetComponent<Enemy>().TakeDamage(10);
               GameObject spark = Instantiate(GameObject.FindObjectOfType<BulletPool>().sparkEffect, other.contacts[0].point, Quaternion.identity);
               Destroy(spark, 0.5f);
           }
           ReturnToPool();
       }

       private void ReturnToPool()
       {
           BulletPool.Instance.ReturnBullet(gameObject);
       }

       void OnDisable()
       {
           CancelInvoke();
       }
   }
   ```

4. **Настрой `Bullet.prefab`:**  
   - Добавь **Rigidbody** (Mass: 1, Constraints: Freeze Rotation X/Y/Z).  
   - Убедись, что есть **Collider** (Sphere или Capsule).  

5. **Настрой и протестируй:**  
   - Нажми **Play**, стреляй — пули движутся физически и сталкиваются с врагами!  

### Уровень 2: Отталкивание врагов  
1. **Обнови `Enemy`:**  
   - Добавь отталкивание в `TakeDamage`:  
     ```csharp
     public virtual void TakeDamage(int damage)
     {
         currentHealth -= damage;
         Debug.Log($"{gameObject.name} получил урон! Осталось здоровья: {currentHealth}");
         Rigidbody rb = GetComponent<Rigidbody>();
         Vector3 pushDirection = (transform.position - GameObject.FindGameObjectWithTag("Player").transform.position).normalized;
         rb.AddForce(pushDirection * 5.0f, ForceMode.Impulse);
         if (currentHealth <= 0)
         {
             Die();
         }
     }
     ```

2. **Настрой `Enemy.prefab` и `Boss.prefab`:**  
   - Добавь **Rigidbody** (Mass: 1, Constraints: Freeze Rotation X/Y/Z, Freeze Position Y).  
   - Убедись, что есть **Collider**.  

3. **Настрой и протестируй:**  
   - Нажми **Play**, стреляй во врагов — они отталкиваются при попадании!  

### Уровень 3: Физика бонусов  
1. **Обнови `HealthBonus`:**  
   - Добавь подпрыгивание при спавне:  
     ```csharp
     void Start()
     {
         GameManager.Instance.AddBonus(this);
         healthBoost = Random.Range(20, 50);
         Rigidbody rb = GetComponent<Rigidbody>();
         rb.AddForce(Vector3.up * 3.0f, ForceMode.Impulse);
         Debug.Log($"Бонус с здоровьем: {healthBoost}, подпрыгнул!");
     }
     ```

2. **Настрой `HealthBonus.prefab`:**  
   - Добавь **Rigidbody** (Mass: 1, Constraints: Freeze Rotation X/Y/Z).  
   - Убедись, что есть **Collider** (Sphere).  

3. **Настрой и протестируй:**  
   - Нажми **Play**, собери бонус — он подпрыгивает при спавне!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Настройки **Rigidbody** на префабах.  
- В **Scene/Game**: Пули движутся физически, враги отталкиваются, бонусы подпрыгивают.  
- В **Console**: Сообщения о силах и попаданиях.  

## 💡 **Квесты: Прокачай физику!**
1. **Базовый квест**:  
   - Увеличь силу отталкивания врагов до 7.0f:  
     ```csharp
     rb.AddForce(pushDirection * 7.0f, ForceMode.Impulse);
     ```  
   - Проверь: враги отлетают дальше!  

2. **Квест на пули**:  
   - Увеличь `bulletSpeed` в `PlayerController` до 15.0f.  
   - Проверь: пули летят быстрее!  

3. **Квест на бонусы**:  
   - Увеличь силу подпрыгивания бонусов до 5.0f.  
   - Проверь: бонусы прыгают выше!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь вращение бонусов:  
     ```csharp
     void Update()
     {
         transform.Rotate(0, 90 * Time.deltaTime, 0);
     }
     ```  
   - Проверь: бонусы вращаются!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если физика не работает:  
  - Сохранил ли код и префабы (**Ctrl+S**)?  
  - Проверь **Rigidbody** Constraints.  
  - Есть ли **Collider**?  
- **Хочешь эпичности?** В `BulletController` добавь:  
  ```csharp
  Debug.Log("Пуля столкнулась!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Объекты не двигаются?** Проверь `Is Kinematic` и силы.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Физика делает бои реалистичными и тактильными, усиливая геймплей. Это ключ к динамике!

## Заключение: Rigidbody — Твой Мастер Сил! 💻
Ты освоил физику, добавив пули и отталкивание. Твой шутер стал динамичным! Следующий шаг — камера, следующая за героем. Продолжай, физик кода!

**Что Далее?**  
- Перейди к [Камера — Следование за Героем](../Advanced/Lesson25_Camera.md) — улучши обзор.  
- Вопросы: [Unity Learn: Rigidbody](https://learn.unity.com/tutorial/rigidbody).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
