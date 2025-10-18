
# 🎯 Урок 10: Raycast — Стрельба по Прицелу!

Привет, юный снайпер кода! 💻 Добро пожаловать на четвёртый уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером прицеливания**, освоив **Raycast** в Unity, чтобы твой герой в топ-даун шутере (*UnityTopDownShooterQuest*) стрелял точно в цель, а не просто вперёд. Raycast — это как лазерный прицел, который проверяет, что находится на пути. Ты сделаешь стрельбу по направлению мыши и добавишь визуальный эффект попадания! Готов попасть в яблочко? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus` из [Урок 9: Теги и Слои](../Intermediate/Lesson9_TagsLayers.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя снайперская лаборатория!  

**Предупреждение**: Raycast требует точной настройки слоёв и камеры. Сохраняй код (**Ctrl+S**) перед тестом, иначе прицел собьётся! Если пули летят не туда, проверь настройки слоёв и камеры. Врубай Play Mode и стреляй как профи!

Готов прицелиться? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Raycast?**
**Raycast** — это метод в Unity, который выпускает "луч" из точки (например, от мыши) и проверяет, с чем он сталкивается. Это идеально для стрельбы по прицелу или проверки препятствий.

В твоём шутере Raycast позволит:  
- Стрелять в направлении курсора мыши.  
- Определять, попала ли пуля во врага или босса.  
- Добавлять эффекты попадания (например, сообщение в консоли).

*Ссылка на документацию*: [Physics.Raycast](https://docs.unity3d.com/ScriptReference/Physics.Raycast.html).

## 🔄 **Зачем это Нужно?**
Raycast делает стрельбу точной: герой стреляет туда, куда ты целишься, а не просто вперёд. В этом квесте ты:  
- Изменишь стрельбу героя на Raycast-основанную.  
- Добавишь визуальный эффект попадания.  
- Сделаешь бой в твоём шутере эпичным!

**Почему это круто?**  
- **Точность**: Стреляй туда, куда смотришь!  
- **Интерактивность**: Raycast делает бой реалистичным.  
- **Для шутера**: Это основа для снайперской стрельбы и прицельных атак!

**Типичные ошибки новичков**:  
- Неправильный слой в `LayerMask` (Raycast игнорирует объекты).  
- Забыл настроить камеру для Raycast.  
- Raycast в `Start` вместо `Update` (нужен каждый кадр).

## ⚙️ **Квест: Стреляй с прицелом**

### Уровень 1: Raycast для стрельбы  
1. **Настрой камеру:**  
   - В **Hierarchy** выбери `Main Camera`, установи позицию (x=0, y=10, z=0), поворот (x=90, y=0, z=0) для топ-даун вида.  

2. **Обнови `PlayerController`:**  
   - Добавь переменные для Raycast и камеры:  
     ```csharp
     [SerializeField] private LayerMask shootableLayer; // Слои для попадания
     private Camera mainCamera; // Главная камера
     ```

3. **Измени стрельбу с Raycast:**  
   - В `Start` добавь получение камеры:  
     ```csharp
     mainCamera = Camera.main;
     ```
   - Замени `ShootWithReload`:  
     ```csharp
     private IEnumerator ShootWithReload()
     {
         canShoot = false;
         Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
         RaycastHit hit;
         if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
         {
             Vector3 direction = (hit.point - transform.position).normalized;
             for (int i = 0; i < bulletsPerShot; i++)
             {
                 GameObject bullet = Instantiate(bulletPrefab, transform.position + direction * 0.5f, Quaternion.identity);
                 bullet.GetComponent<Rigidbody>().velocity = direction * bulletSpeed;
                 Debug.Log(playerName + " выстрелил пулей " + (i + 1) + " в сторону " + hit.point + "!");
             }
         }
         currentAmmo -= bulletsPerShot;
         Debug.Log("Осталось патронов: " + currentAmmo);
         yield return new WaitForSeconds(reloadTime);
         canShoot = true;
     }
     ```

4. **Полный код `PlayerController`:**  
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
       [SerializeField] private LayerMask shootableLayer;
       private int currentHealth;
       private int currentAmmo;
       private bool canShoot = true;
       private Camera mainCamera;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
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
           Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
           RaycastHit hit;
           if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
           {
               Vector3 direction = (hit.point - transform.position).normalized;
               for (int i = 0; i < bulletsPerShot; i++)
               {
                   GameObject bullet = Instantiate(bulletPrefab, transform.position + direction * 0.5f, Quaternion.identity);
                   bullet.GetComponent<Rigidbody>().velocity = direction * bulletSpeed;
                   Debug.Log(playerName + " выстрелил пулей " + (i + 1) + " в сторону " + hit.point + "!");
               }
           }
           currentAmmo -= bulletsPerShot;
           Debug.Log("Осталось патронов: " + currentAmmo);
           yield return new WaitForSeconds(reloadTime);
           canShoot = true;
       }
   }
   ```

5. **Настрой и протестируй:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Inspector** для `Player` выбери `EnemyLayer` и `BossLayer` в поле `Shootable Layer`.  
   - Нажми **Play**, двигай героя (WASD), целься мышью, стреляй (**Space**) — пули летят к точке прицела!  
   - В **Console**: сообщения о выстрелах и направлении.  

### Уровень 2: Эффект попадания  
1. **Обнови `BulletController`:**  
   - Добавь эффект для Raycast-попадания:  
     ```csharp
     void Start()
     {
         Destroy(gameObject, 3.0f); // Уничтожаем пулю через 3 секунды
     }

     void Update()
     {
         Ray ray = new Ray(transform.position, transform.GetComponent<Rigidbody>().velocity.normalized);
         RaycastHit hit;
         if (Physics.Raycast(ray, out hit, 0.5f, LayerMask.GetMask("EnemyLayer", "BossLayer")))
         {
             if (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss"))
             {
                 hit.collider.GetComponent<EnemyStats>().TakeDamage(damage);
                 Debug.Log("Пуля попала в " + hit.collider.name + " с помощью Raycast!");
                 Destroy(gameObject);
             }
         }
     }
     ```

2. **Полный код `BulletController`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class BulletController : MonoBehaviour
   {
       [SerializeField] private int damage = 20;

       void Start()
       {
           Destroy(gameObject, 3.0f);
       }

       void Update()
       {
           Ray ray = new Ray(transform.position, transform.GetComponent<Rigidbody>().velocity.normalized);
           RaycastHit hit;
           if (Physics.Raycast(ray, out hit, 0.5f, LayerMask.GetMask("EnemyLayer", "BossLayer")))
           {
               if (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss"))
               {
                   hit.collider.GetComponent<EnemyStats>().TakeDamage(damage);
                   Debug.Log("Пуля попала в " + hit.collider.name + " с помощью Raycast!");
                   Destroy(gameObject);
               }
           }
       }

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Enemy") || other.CompareTag("Boss"))
           {
               other.GetComponent<EnemyStats>().TakeDamage(damage);
               Debug.Log("Пуля попала в " + other.name + "!");
               Destroy(gameObject);
           }
       }
   }
   ```

3. **Протестируй попадания:**  
   - Сохрани код (**Ctrl+S**).  
   - Нажми **Play**, стреляй по врагам — пули уничтожают врагов через Raycast или триггер!  
   - В **Console**: сообщения о попаданиях.  

### Уровень 3: Визуальный прицел  
1. **Создай визуальный эффект:**  
   - В **Hierarchy** создай **3D Object > Sphere**, назови `Crosshair`, масштаб (x=0.1, y=0.1, z=0.1).  
   - Создай материал (**Project > Create > Material**, назови `CrosshairMat`, цвет красный).  
   - Прикрепи материал к `Crosshair`.  

2. **Создай скрипт для прицела:**  
   - В **Project** создай **C# Script**, назови `CrosshairController`.  
   - Добавь код:  
     ```csharp
     using System.Collections;
     using System.Collections.Generic;
     using UnityEngine;

     public class CrosshairController : MonoBehaviour
     {
         private Camera mainCamera;

         void Start()
         {
             mainCamera = Camera.main;
         }

         void Update()
         {
             Ray ray = mainCamera.ScreenPointToRay(Input.mousePosition);
             RaycastHit hit;
             if (Physics.Raycast(ray, out hit, 100f, LayerMask.GetMask("Default")))
             {
                 transform.position = new Vector3(hit.point.x, 0.1f, hit.point.z);
             }
         }
     }
     ```

3. **Настрой и протестируй:**  
   - Прикрепи `CrosshairController` к `Crosshair`.  
   - Нажми **Play**, двигай мышь — прицел следует за курсором!  
   - Стреляй (**Space**) — пули летят к прицелу.  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поле `Shootable Layer` для `Player`, настройки пули и прицела.  
- В **Scene/Game**: Пули летят к точке прицела, прицел движется по плоскости.  
- В **Console**: Сообщения о выстрелах и попаданиях.  

## 💡 **Квесты: Прокачай прицел!**
1. **Базовый квест**:  
   - В **Inspector** для `Player` установи `Bullet Speed` = 15, `Bullets Per Shot` = 5.  
   - Проверь: пули летят быстрее и больше!  

2. **Квест на точность**:  
   - В `BulletController` увеличь дальность Raycast до 1.0f (в `Update`).  
   - Проверь: пули точнее попадают в цель.  

3. **Квест с боссом**:  
   - Убедись, что `Boss.prefab` работает с Raycast (тег `Boss`, слой `BossLayer`).  
   - Стреляй по боссу — он получает половинный урон!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `PlayerController` добавь индикатор попадания:  
     ```csharp
     if (Physics.Raycast(ray, out hit, 100f, shootableLayer))
     {
         Vector3 direction = (hit.point - transform.position).normalized;
         for (int i = 0; i < bulletsPerShot; i++)
         {
             GameObject bullet = Instantiate(bulletPrefab, transform.position + direction * 0.5f, Quaternion.identity);
             bullet.GetComponent<Rigidbody>().velocity = direction * bulletSpeed;
             Debug.Log(playerName + " выстрелил пулей " + (i + 1) + " в сторону " + hit.point + "!");
         }
         if (hit.collider != null && (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss")))
         {
             Debug.Log("Прицел нацелен на " + hit.collider.name + "!");
         }
     }
     ```  
   - Проверь: консоль показывает, на кого нацелен прицел!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если пули летят не туда:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Правильно ли настроен `Shootable Layer`?  
  - Проверь позицию и поворот камеры.  
- **Хочешь эпичности?** В `CrosshairController` добавь:  
  ```csharp
  Debug.Log("Прицел на позиции: " + transform.position);
  ```  
  - Увидишь координаты прицела!  
- **Пули не попадают?** Убедись, что слои `EnemyLayer` и `BossLayer` включены в `shootableLayer`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Raycast делает стрельбу точной и интерактивной, превращая твой шутер в настоящий снайперский бой! Это основа для прицельных атак и эпичных сражений.

## Заключение: Raycast — Твой Снайперский Прицел! 💻
Ты освоил Raycast, сделав стрельбу точной и добавив прицел. Твой шутер становится профессиональным! Следующий шаг — интерфейс для отображения очков и здоровья. Продолжай, снайпер кода!

**Что Далее?**  
- Перейди к [UI — Показывай Очки и Здоровье](../Intermediate/Lesson11_UI.md) — добавь интерфейс.  
- Вопросы: [Unity Learn: Raycasts](https://learn.unity.com/tutorial/raycasts-in-unity).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
