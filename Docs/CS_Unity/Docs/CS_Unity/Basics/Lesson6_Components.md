
# 🧱 Урок 6: Компоненты GameObject — Управляй Объектами!

Привет, юный архитектор арены! 💻 Добро пожаловать на шестой уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером объектов**, освоив **компоненты GameObject** в Unity, чтобы управлять физикой, рендерингом и поведением в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Компоненты — это как модули на космическом корабле: каждый отвечает за что-то своё (движение, внешний вид, столкновения). Ты добавишь физику для пуль и врагов, чтобы они сталкивались! Готов строить арену? Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner` из [Урок 5: Циклы](../Basics/Lesson5_Loops.md).  
- Префабы `Bullet` и `Enemy` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя стройплощадка кода!  

**Предупреждение**: Компоненты требуют правильной настройки в **Inspector**. Сохраняй код (**Ctrl+S**) перед тестом, иначе физика не сработает! Если пули не сталкиваются, проверь, есть ли у объектов `Collider`. Врубай Play Mode и строй свою арену!  

Готов управлять объектами, как босс? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Компоненты GameObject?**
**GameObject** — это объект в Unity (герой, враг, пуля), а **компоненты** — его "модули", которые задают поведение:  
- **Transform**: Позиция, поворот, масштаб.  
- **Rigidbody**: Физика (движение, столкновения).  
- **Renderer**: Внешний вид (материалы, цвета).  
- **Collider**: Зона столкновений.  

В твоём шутере ты добавишь `Rigidbody` и `Collider` к пулям и врагам, чтобы пули наносили урон при попадании!

*Ссылка на документацию*: [GameObject](https://docs.unity3d.com/ScriptReference/GameObject.html).

## 🔄 **Зачем это Нужно?**
Компоненты — это строительные блоки твоей игры. Они позволяют объектам взаимодействовать: пули сталкиваются с врагами, герой уворачивается. В этом квесте ты:  
- Настроишь физику для пуль и врагов.  
- Добавишь столкновения, чтобы пули наносили урон.  
- Увидишь, как объекты оживают на арене!  

**Почему это круто?**  
- **Реализм**: Физика делает столкновения естественными.  
- **Контроль**: Компоненты дают доступ к объектам из кода.  
- **Для шутера**: Это основа для системы попаданий и боя!  

**Типичные ошибки новичков**:  
- Забыл добавить `Rigidbody` или `Collider`.  
- Неправильный тип коллайдера (например, 2D вместо 3D).  
- Не указал `Is Trigger` для событий столкновений.  

## ⚙️ **Квест: Строй арену с компонентами**

### Уровень 1: Физика для пуль  
1. **Настрой префаб пули:**  
   - Открой `Bullet.prefab` в `Assets/Prefabs`.  
   - Убедись, что есть **Rigidbody** (`Use Gravity` выключено).  
   - Добавь **Sphere Collider** (**Add Component > Sphere Collider**).  

2. **Создай скрипт для пули:**  
   - В **Project** создай **C# Script**, назови `BulletController`.  
   - Добавь код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class BulletController : MonoBehaviour
     {
         [SerializeField] private int damage = 20; // Урон пули

         void OnTriggerEnter(Collider other)
         {
             if (other.CompareTag("Enemy"))
             {
                 other.GetComponent<EnemyStats>().TakeDamage(damage);
                 Debug.Log("Пуля попала в " + other.name + "!");
                 Destroy(gameObject);
             }
         }
     }
     ```

3. **Настрой пулю:**  
   - Открой `Bullet.prefab`, добавь **BulletController** (**Add Component**).  
   - В **Inspector** для `Sphere Collider` включи `Is Trigger`.  
   - Сохрани префаб.  

### Уровень 2: Физика для врагов  
1. **Настрой префаб врага:**  
   - Открой `Enemy.prefab`.  
   - Убедись, что есть **Box Collider** (**Add Component > Box Collider**).  
   - Добавь **Rigidbody** (`Use Gravity` выключено, `Constraints > Freeze Rotation` включены для всех осей).  
   - В **Inspector** для `EnemyStats` установи тег `Enemy` (**Tag > Enemy**).  

2. **Проверь `EnemyStats`:**  
   - Убедись, что код включает атаку и цвет:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class EnemyStats : MonoBehaviour
     {
         [SerializeField] private int maxHealth = 100;
         [SerializeField] private string enemyName = "CyberDrone";
         [SerializeField] private float moveSpeed = 2.5f;
         [SerializeField] private GameObject player;
         [SerializeField] private float attackDistance = 3.0f;
         private int currentHealth;
         private Renderer enemyRenderer;

         void Start()
         {
             currentHealth = maxHealth;
             enemyRenderer = GetComponent<Renderer>();
             Debug.Log(enemyName + " готов к бою! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
             TakeDamage(60);
         }

         void Update()
         {
             transform.Translate(Vector3.right * moveSpeed * Time.deltaTime);
             if (currentHealth < 50)
             {
                 enemyRenderer.material.color = Color.red;
                 Debug.Log(enemyName + " ослаб! Здоровье: " + currentHealth);
                 moveSpeed = moveSpeed * 0.5f;
             }
             else
             {
                 enemyRenderer.material.color = Color.white;
             }

             float distanceToPlayer = Vector3.Distance(transform.position, player.transform.position);
             if (distanceToPlayer < attackDistance)
             {
                 Debug.Log(enemyName + " атакует " + player.GetComponent<PlayerController>().playerName + "!");
                 player.GetComponent<PlayerController>().TakeDamage(10);
             }
         }

         public void TakeDamage(int damage)
         {
             currentHealth -= damage;
             Debug.Log(enemyName + " получил урон! Осталось здоровья: " + currentHealth);
             if (currentHealth <= 0)
             {
                 Debug.Log(enemyName + " уничтожен!");
                 Destroy(gameObject);
             }
         }
     }
     ```

3. **Протестируй столкновения:**  
   - В **Hierarchy** убедись, что `Player`, `Spawner`, и префабы настроены.  
   - Нажми **Play**, двигай героя, стреляй (**Space**) в сторону врагов.  
   - В **Scene/Game**: Пули исчезают при попадании, враги краснеют и уничтожаются!  
   - В **Console**: Сообщения о попаданиях и уроне.  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для пуль и врагов (`Damage`, `Enemy` тег).  
- В **Scene/Game**: Пули сталкиваются с врагами, враги исчезают при нулевом здоровье.  
- В **Console**: Сообщения о попаданиях, уроне и атаках.  

## 💡 **Квесты: Прокачай арену!**
1. **Базовый квест**:  
   - В **Inspector** для `Bullet.prefab` установи `Damage` = 30.  
   - Для `Enemy.prefab` установи `Max Health` = 50.  
   - Нажми **Play**, стреляй — враги уничтожаются быстрее!  

2. **Квест на физику**:  
   - В `Bullet.prefab` увеличь радиус `Sphere Collider` до 0.3.  
   - Проверь, что пули легче попадают по врагам.  

3. **Квест с несколькими врагами**:  
   - В `EnemySpawner` установи `Enemies To Spawn` = 8.  
   - Нажми **Play**, стреляй по врагам — проверь, что все исчезают при попадании!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `BulletController` добавь очки за попадание:  
     ```csharp
     [SerializeField] private int scorePerHit = 50; // Очки за попадание
     ```
   - В `OnTriggerEnter`:  
     ```csharp
     if (other.CompareTag("Enemy"))
     {
         other.GetComponent<EnemyStats>().TakeDamage(damage);
         Debug.Log("Пуля попала в " + other.name + "! + " + scorePerHit + " очков!");
         Destroy(gameObject);
     }
     ```
   - В `PlayerController` добавь счётчик очков:  
     ```csharp
     [SerializeField] private int totalScore = 0; // Общий счёт
     ```
   - В `Update`:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.Space) && currentAmmo >= bulletsPerShot)
     {
         for (int i = 0; i < bulletsPerShot; i++)
         {
             GameObject bullet = Instantiate(bulletPrefab, transform.position + Vector3.forward, Quaternion.identity);
             bullet.GetComponent<Rigidbody>().velocity = Vector3.forward * bulletSpeed;
             Debug.Log(playerName + " выстрелил пулей " + (i + 1) + "!");
         }
         currentAmmo -= bulletsPerShot;
         Debug.Log("Осталось патронов: " + currentAmmo);
     }
     ```
   - В **Inspector** установи `Score Per Hit` = 100 для `Bullet.prefab`.  
   - Проверь: при попадании в консоли отображаются очки!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если пули не попадают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Есть ли `Collider` и `Rigidbody` на пуле/враге?  
  - Проверь, установлен ли тег `Enemy` на враге.  
- **Хочешь эпичности?** В `BulletController` добавь:  
  ```csharp
  Debug.Log("Пуля на позиции: " + transform.position);
  ```
  - Увидишь траекторию пули!  
- **Пули не исчезают?** Убедись, что `Is Trigger` включено для `Sphere Collider`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Компоненты — основа взаимодействия: пули наносят урон, враги сталкиваются. Это сердце системы боя твоего шутера!  

## Заключение: Компоненты — Твои Строительные Блоки! 💻
Ты освоил компоненты, добавил физику и столкновения. Твой шутер становится настоящей ареной! Следующий шаг — коллайдеры для точного управления попаданиями. Продолжай, архитектор кода!  

**Что Далее?**  
- Перейди к [Коллайдеры — Столкновения и Урон](../Intermediate/Lesson7_Collisions.md) — улучшай бои.  
- Вопросы: [Unity Learn: GameObjects](https://learn.unity.com/tutorial/working-with-gameobjects).  

[Назад к оглавлению](../CS_Unity.md)  

*Author: [DGGelik](https://github.com/DGGelik). Date: October 18, 2025.*
