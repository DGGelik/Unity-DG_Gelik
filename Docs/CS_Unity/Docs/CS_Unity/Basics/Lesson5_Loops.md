
# 🔁 Урок 5: Циклы — Повторяй Действия для Стрельбы!

Привет, юный стрелок кода! 💻 Добро пожаловать на пятый уровень *Части 2* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером автоматизации**, освоив **циклы** (`for`, `while`) в C#, чтобы твой герой в топ-даун шутере (*UnityTopDownShooterQuest*) стрелял очередями, а враги спавнились волнами. Циклы — это как автоматическое оружие: повторяют действия без лишнего кода. Готов зарядить обойму кода? Время на квест: 20–25 минут.  

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController` и `EnemyStats` из [Урок 4: Условные Операторы](../Basics/Lesson4_IfElse.md).  
- Настроенный редактор кода ([Настройка Unity и Редактора Кода](../Preparation/Lesson2_SetupEditor.md)).  
- Папка Scripts в Assets — твоя кузница кода!  

**Предупреждение**: Циклы требуют осторожности — бесконечный цикл (`while(true)`) зависнет Unity! Проверяй условия выхода и сохраняй код (**Ctrl+S**) перед тестом. Если враги не спавнятся или герой не стреляет, проверь консоль. Врубай Play Mode и заряжай арену!  

Готов автоматизировать бой? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Циклы?**
**Циклы** — это команды, которые повторяют код несколько раз. В C# есть два главных типа:  
- **`for`**: Повторяет код фиксированное число раз (например, "выстрели 5 пуль").  
- **`while`**: Повторяет, пока условие истинно (например, "стреляй, пока есть патроны").  

В твоём шутере циклы помогут:  
- Герою стрелять очередями.  
- Спавнить волны врагов.  
- Проверять несколько объектов.  

*Ссылка на документацию*: [C# Iteration Statements](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements).

## 🔄 **Зачем это Нужно?**
Циклы экономят время: вместо написания "выстрели, выстрели, выстрели" ты пишешь один цикл. В этом квесте ты:  
- Добавишь стрельбу героя очередями с помощью `for`.  
- Заставишь врагов спавниться волной с помощью `while`.  
- Увидишь, как циклы делают игру динамичной!  

**Почему это круто?**  
- **Автоматизация**: Один цикл — и герой палит очередями!  
- **Масштаб**: Спавнь 10 врагов одной строкой.  
- **Для шутера**: Это основа для стрельбы, волн врагов и эпичных сражений!  

**Типичные ошибки новичков**:  
- Бесконечный цикл (нет условия выхода).  
- Неправильный счётчик в `for` (например, `i--` вместо `i++`).  
- Забыл обновить переменную в `while`.  

## ⚙️ **Квест: Заряди стрельбу и волны врагов**

### Уровень 1: Стрельба героя очередями  
1. **Создай префаб пули:**  
   - В **Hierarchy** создай **3D Object > Sphere**, назови `Bullet`.  
   - Установи масштаб (`Scale`): x=0.2, y=0.2, z=0.2.  
   - Щёлкни правой кнопкой на `Bullet` > **Create Prefab**, сохрани в `Assets/Prefabs/Bullet.prefab`.  
   - Удали `Bullet` из **Hierarchy**.  

2. **Обнови `PlayerController`:**  
   - Добавь переменные и цикл для стрельбы:  
     ```csharp
     [SerializeField] private GameObject bulletPrefab; // Префаб пули
     [SerializeField] private int bulletsPerShot = 3; // Пули в очереди
     [SerializeField] private float bulletSpeed = 10.0f; // Скорость пули
     ```

3. **Добавь стрельбу в Update:**  
   - В `Update` добавь:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.Space))
     {
         for (int i = 0; i < bulletsPerShot; i++)
         {
             GameObject bullet = Instantiate(bulletPrefab, transform.position + Vector3.forward, Quaternion.identity);
             bullet.GetComponent<Rigidbody>().velocity = Vector3.forward * bulletSpeed;
             Debug.Log(playerName + " выстрелил пулей " + (i + 1) + "!");
         }
     }
     ```

4. **Полный код `PlayerController`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class PlayerController : MonoBehaviour
   {
       [SerializeField] private float moveSpeed = 5.0f; // Скорость героя
       [SerializeField] private string playerName = "Hero"; // Имя героя
       [SerializeField] private int maxHealth = 100; // Максимальное здоровье
       [SerializeField] private GameObject bulletPrefab; // Префаб пули
       [SerializeField] private int bulletsPerShot = 3; // Пули в очереди
       [SerializeField] private float bulletSpeed = 10.0f; // Скорость пули
       private int currentHealth; // Текущее здоровье

       void Start()
       {
           currentHealth = maxHealth;
           Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60);
       }

       void Update()
       {
           float moveX = Input.GetAxis("Horizontal");
           float moveY = Input.GetAxis("Vertical");
           Vector3 movement = new Vector3(moveX, 0, moveY);
           transform.Translate(movement * moveSpeed * Time.deltaTime);

           if (currentHealth < 50)
           {
               moveSpeed = 2.0f;
               Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
           }
           else
           {
               moveSpeed = 5.0f;
           }

           if (Input.GetKeyDown(KeyCode.Space))
           {
               for (int i = 0; i < bulletsPerShot; i++)
               {
                   GameObject bullet = Instantiate(bulletPrefab, transform.position + Vector3.forward, Quaternion.identity);
                   bullet.GetComponent<Rigidbody>().velocity = Vector3.forward * bulletSpeed;
                   Debug.Log(playerName + " выстрелил пулей " + (i + 1) + "!");
               }
           }
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
           if (currentHealth <= 0)
           {
               Debug.Log(playerName + " повержен!");
               Destroy(gameObject);
           }
       }
   }
   ```

5. **Настрой и протестируй:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** выбери `Player`, перетащи `Bullet.prefab` в поле `Bullet Prefab` в **Inspector**.  
   - Добавь **Rigidbody** к `Bullet.prefab` (в **Project**): открой префаб, **Add Component > Rigidbody**, убедись, что `Use Gravity` выключено.  
   - Нажми **Play**, двигай героя WASD, жми **Space** — три пули вылетают вперёд!  
   - В **Console**: сообщения о выстрелах.  

### Уровень 2: Спавн волны врагов  
1. **Создай префаб врага:**  
   - В **Hierarchy** выбери `Enemy` (куб с `EnemyStats`).  
   - Щёлкни правой кнопкой > **Create Prefab**, сохрани в `Assets/Prefabs/Enemy.prefab`.  
   - Удали `Enemy` из **Hierarchy**.  

2. **Создай скрипт спавнера:**  
   - В **Project** создай **C# Script**, назови `EnemySpawner`.  
   - Добавь код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class EnemySpawner : MonoBehaviour
     {
         [SerializeField] private GameObject enemyPrefab; // Префаб врага
         [SerializeField] private int enemiesToSpawn = 5; // Количество врагов
         [SerializeField] private float spawnDelay = 1.0f; // Задержка между спавнами

         void Start()
         {
             int spawnedEnemies = 0;
             while (spawnedEnemies < enemiesToSpawn)
             {
                 Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
                 Debug.Log("Спавн врага " + (spawnedEnemies + 1));
                 spawnedEnemies++;
             }
         }
     }
     ```

3. **Настрой спавнер:**  
   - В **Hierarchy** создай **Create Empty**, назови `Spawner`.  
   - Прикрепи `EnemySpawner` к `Spawner`.  
   - Перетащи `Enemy.prefab` в поле `Enemy Prefab` в **Inspector**.  
   - Убедись, что в `Enemy.prefab` поле `Player` ссылается на `Player` (перетащи капсулу).  
   - Нажми **Play**: 5 врагов спавнятся в линию!  

## 💻 **Что ты увидишь?**
- В **Scene/Game**: Герой стреляет тремя пулями по **Space**, 5 врагов появляются в линию.  
- В **Console**: Сообщения о выстрелах и спавне врагов.  
- В **Inspector**: Поля для настройки стрельбы и спавна.  

## 💡 **Квесты: Прокачай бой!**
1. **Базовый квест**:  
   - В **Inspector** для `Player` установи `Bullets Per Shot` = 5, `Bullet Speed` = 15.  
   - Нажми **Play**, жми **Space** — проверь, что вылетает 5 быстрых пуль!  

2. **Квест на спавн**:  
   - В `EnemySpawner` установи `Enemies To Spawn` = 3, `Spawn Delay` = 0.5.  
   - Проверь, что спавнится 3 врага с меньшей задержкой.  

3. **Квест с двумя спавнерами**:  
   - Создай второй объект `Spawner2`, прикрепи `EnemySpawner`.  
   - Установи: `Enemies To Spawn` = 4, позиция `Spawner2` (x=0, y=0, z=5).  
   - Нажми **Play**, убедись, что спавнится 9 врагов (5 + 4)!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `PlayerController` добавь ограничение патронов:  
     ```csharp
     [SerializeField] private int maxAmmo = 10; // Максимум патронов
     private int currentAmmo; // Текущие патроны
     ```
   - В `Start`:  
     ```csharp
     currentAmmo = maxAmmo;
     ```
   - В `Update`, измени стрельбу:  
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
     else if (Input.GetKeyDown(KeyCode.Space))
     {
         Debug.Log("Нет патронов!");
     }
     ```
   - В **Inspector** установи `Max Ammo` = 6, `Bullets Per Shot` = 3.  
   - Проверь: герой стреляет 2 раза (6 патронов), потом "Нет патронов!".  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если пули не летят или враги не спавнятся:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Прикреплён ли префаб пули/врага в **Inspector**?  
  - Проверь, есть ли `Rigidbody` на пуле.  
- **Хочешь эпичности?** В `EnemySpawner` добавь:  
  ```csharp
  Debug.Log("Враг " + (spawnedEnemies + 1) + " на позиции: " + new Vector3(spawnedEnemies * 2.0f, 0, 0));
  ```
  - Увидишь позиции спавна!  
- **Бесконечный цикл?** Проверь условие `while` в `EnemySpawner`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Циклы — твоя обойма для стрельбы и спавна врагов. Они делают игру динамичной: очереди пуль и волны врагов — основа эпичных сражений!  

## Заключение: Циклы — Твой Автомат для Боя! 💻
Ты научился стрелять очередями и спавнить врагов волнами. Твой шутер оживает! Следующий шаг — компоненты GameObject, чтобы управлять объектами. Продолжай, стрелок кода!  

**Что Далее?**  
- Перейди к [Компоненты GameObject — Управляй Объектами](./Lesson6_Components.md) — управляй объектами.  
- Вопросы: [Unity Learn: Scripting Loops](https://learn.unity.com/tutorial/introduction-to-scripting-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
