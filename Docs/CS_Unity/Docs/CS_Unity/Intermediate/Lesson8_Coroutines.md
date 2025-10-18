
# ⏳ Урок 8: Корутины — Задержки для Перезарядки!

Привет, юный мастер времени! 💻 Добро пожаловать на второй уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **повелителем задержек**, освоив **корутины** в C#, чтобы добавить перезарядку оружия и волновой спавн врагов в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Корутины — это как таймеры, которые приостанавливают код, не замораживая игру. Готов управлять ритмом боя? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus` из [Урок 7: Коллайдеры](../Intermediate/Lesson7_Collisions.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя кузница времени!  

**Предупреждение**: Корутины требуют `IEnumerator` и `StartCoroutine`. Сохраняй код (**Ctrl+S**) перед тестом, иначе задержки не сработают! Если стрельба или спавн сбоят, проверь вызовы корутин в **Console**. Врубай Play Mode и управляй темпом битвы!

Готов овладеть временем? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Корутины?**
**Корутины** — это специальные методы в Unity, которые позволяют приостанавливать и возобновлять выполнение кода, идеально подходя для задержек (например, перезарядка оружия) или последовательностей (например, волны врагов). В отличие от `Update`, они не выполняются каждый кадр, экономя производительность.

В твоём шутере корутины помогут:  
- Добавить задержку перезарядки для стрельбы героя.  
- Спавнить врагов волнами с паузами.  

*Ссылка на документацию*: [Корутины](https://docs.unity3d.com/ScriptReference/Coroutine.html).

## 🔄 **Зачем это Нужно?**
Корутины делают игру отполированной: никаких мгновенных выстрелов или спама врагов. В этом квесте ты:  
- Добавишь задержку перезарядки для оружия героя.  
- Настроишь спавн врагов волнами с паузами.  
- Увидишь, как арена пульсирует от действий!

**Почему это круто?**  
- **Реализм**: Перезарядка делает стрельбу тактической.  
- **Контроль**: Задержки спавна создают эпичные волны.  
- **Для шутера**: Это задаёт ритм для напряжённых сражений!

**Типичные ошибки новичков**:  
- Забыл `IEnumerator` или `StartCoroutine`.  
- Неправильные значения задержек (секунды, а не кадры).  
- Корутина не остановлена, что вызывает наложения.

## ⚙️ **Квест: Управляй временем на арене**

### Уровень 1: Задержка перезарядки для героя  
1. **Обнови `PlayerController`:**  
   - Добавь переменные для перезарядки:  
     ```csharp
     [SerializeField] private float reloadTime = 1.0f; // Время перезарядки
     private bool canShoot = true; // Разрешена ли стрельба
     ```

2. **Измени стрельбу с корутиной:**  
   - В `Update` замени код стрельбы:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.Space) && canShoot)
     {
         StartCoroutine(ShootWithReload());
     }
     ```
   - Добавь корутину:  
     ```csharp
     private IEnumerator ShootWithReload()
     {
         canShoot = false;
         for (int i = 0; i < bulletsPerShot; i++)
         {
             GameObject bullet = Instantiate(bulletPrefab, transform.position + Vector3.forward, Quaternion.identity);
             bullet.GetComponent<Rigidbody>().velocity = Vector3.forward * bulletSpeed;
             Debug.Log(playerName + " выстрелил пулей " + (i + 1) + "!");
         }
         currentAmmo -= bulletsPerShot;
         Debug.Log("Осталось патронов: " + currentAmmo);
         yield return new WaitForSeconds(reloadTime);
         canShoot = true;
     }
     ```

3. **Полный код `PlayerController`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class PlayerController : MonoBehaviour
   {
       [SerializeField] private float moveSpeed = 5.0f;
       [SerializeField] private string playerName = "Hero";
       [SerializeField] private int maxHealth = 100;
       [SerializeField] private GameObject bulletPrefab;
       [SerializeField] private int bulletsPerShot = 3;
       [SerializeField] private float bulletSpeed = 10.0f;
       [SerializeField] private int maxAmmo = 10;
       [SerializeField] private float reloadTime = 1.0f;
       private int currentHealth;
       private int currentAmmo;
       private bool canShoot = true;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
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

           if (Input.GetKeyDown(KeyCode.Space) && canShoot)
           {
               StartCoroutine(ShootWithReload());
           }
           else if (Input.GetKeyDown(KeyCode.Space))
           {
               Debug.Log("Нельзя стрелять! Перезарядка или нет патронов!");
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

       private IEnumerator ShootWithReload()
       {
           canShoot = false;
           for (int i = 0; i < bulletsPerShot; i++)
           {
               GameObject bullet = Instantiate(bulletPrefab, transform.position + Vector3.forward, Quaternion.identity);
               bullet.GetComponent<Rigidbody>().velocity = Vector3.forward * bulletSpeed;
               Debug.Log(playerName + " выстрелил пулей " + (i + 1) + "!");
           }
           currentAmmo -= bulletsPerShot;
           Debug.Log("Осталось патронов: " + currentAmmo);
           yield return new WaitForSeconds(reloadTime);
           canShoot = true;
       }
   }
   ```

4. **Протестируй стрельбу:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `Bullet.prefab` привязан в **Inspector** для `Player`.  
   - Нажми **Play**, стреляй (**Space**) — пули вылетают, затем 1-секундная задержка перед следующим выстрелом!  

### Уровень 2: Волновой спавн врагов  
1. **Обнови `EnemySpawner`:**  
   - Замени `while` на корутину:  
     ```csharp
     void Start()
     {
         StartCoroutine(SpawnWave());
     }

     private IEnumerator SpawnWave()
     {
         int spawnedEnemies = 0;
         while (spawnedEnemies < enemiesToSpawn)
         {
             Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
             Debug.Log("Спавн врага " + (spawnedEnemies + 1));
             spawnedEnemies++;
             yield return new WaitForSeconds(spawnDelay);
         }
         Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
         Debug.Log("Спавн бонуса здоровья!");
     }
     ```

2. **Полный код `EnemySpawner`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemySpawner : MonoBehaviour
   {
       [SerializeField] private GameObject enemyPrefab;
       [SerializeField] private GameObject bonusPrefab;
       [SerializeField] private int enemiesToSpawn = 5;
       [SerializeField] private float spawnDelay = 1.0f;

       void Start()
       {
           StartCoroutine(SpawnWave());
       }

       private IEnumerator SpawnWave()
       {
           int spawnedEnemies = 0;
           while (spawnedEnemies < enemiesToSpawn)
           {
               Instantiate(enemyPrefab, new Vector3(spawnedEnemies * 2.0f, 0, 0), Quaternion.identity);
               Debug.Log("Спавн врага " + (spawnedEnemies + 1));
               spawnedEnemies++;
               yield return new WaitForSeconds(spawnDelay);
           }
           Instantiate(bonusPrefab, new Vector3(0, 0, 5), Quaternion.identity);
           Debug.Log("Спавн бонуса здоровья!");
       }
   }
   ```

3. **Протестируй волны:**  
   - Сохрани код (**Ctrl+S**).  
   - Убедись, что `Enemy.prefab` и `HealthBonus.prefab` привязаны в `Spawner`.  
   - Нажми **Play**: враги спавнятся по одному с задержкой 1 секунда, затем появляется бонус!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для времени перезарядки и задержки спавна.  
- В **Scene/Game**: Герой стреляет с задержкой, враги появляются волнами.  
- В **Console**: Сообщения о выстрелах, спавне и бонусах.  

## 💡 **Квесты: Прокачай темп битвы!**
1. **Базовый квест**:  
   - В **Inspector** для `Player` установи `Reload Time` = 0.5.  
   - Для `EnemySpawner` установи `Spawn Delay` = 0.3.  
   - Нажми **Play**, проверь более быструю стрельбу и спавн!  

2. **Квест на перезарядку**:  
   - В `PlayerController` установи `reloadTime` = 2.0 в **Inspector**.  
   - Проверь: стрельба с большей задержкой.  

3. **Квест на волны**:  
   - В `EnemySpawner` установи `Enemies To Spawn` = 10.  
   - Проверь: 10 врагов спавнятся с задержками, затем бонус!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `PlayerController` добавь перезарядку патронов:  
     ```csharp
     [SerializeField] private float ammoReloadTime = 3.0f;
     ```
   - В `Update` добавь:  
     ```csharp
     if (Input.GetKeyDown(KeyCode.R) && currentAmmo < maxAmmo)
     {
         StartCoroutine(ReloadAmmo());
     }
     ```
   - Добавь корутину:  
     ```csharp
     private IEnumerator ReloadAmmo()
     {
         canShoot = false;
         Debug.Log(playerName + " перезаряжает патроны...");
         yield return new WaitForSeconds(ammoReloadTime);
         currentAmmo = maxAmmo;
         Debug.Log(playerName + " патроны перезаряжены! Патронов: " + currentAmmo);
         canShoot = true;
     }
     ```  
   - Проверь: нажми **R** для перезарядки патронов за 3 секунды!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если стрельба или спавн не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Правильно ли вызваны `StartCoroutine`?  
  - Проверь значения `reloadTime` и `spawnDelay`.  
- **Хочешь эпичности?** В `SpawnWave` добавь:  
  ```csharp
  Debug.Log("Следующий враг через " + spawnDelay + " секунд!");
  ```  
  - Увидишь тайминг спавна в **Console**!  
- **Нет задержек?** Убедись, что `yield return` используется корректно.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Корутины добавляют ритм сражениям: перезарядка и волны делают бой стратегическим. Это ключ к отполированному шутеру!

## Заключение: Корутины — Твой Таймер Боя! 💻
Ты освоил корутины, добавив перезарядку и волновой спавн. Твой шутер пульсирует действием! Следующий шаг — теги и слои для организации арены. Продолжай, мастер времени!

**Что Далее?**  
- Перейди к [Теги и Слои — Отдели Героя от Врагов](../Intermediate/Lesson9_TagsLayers.md) — организуй объекты.  
- Вопросы: [Unity Learn: Корутины](https://learn.unity.com/tutorial/coroutines).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
