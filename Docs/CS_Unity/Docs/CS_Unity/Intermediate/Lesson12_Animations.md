
# 🎬 Урок 12: Анимации — Оживи Бой!

Привет, юный режиссёр арены! 💻 Добро пожаловать на шестой уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером анимаций**, освоив систему **Animator** в Unity, чтобы оживить твоего героя и врагов в топ-даун шутере (*UnityTopDownShooterQuest*). Анимации добавляют зрелищности: от движения героя до взрывов врагов. Ты создашь анимацию движения героя и эффект уничтожения врага! Готов снять эпичный боевик? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus`, `CrosshairController`, `HealthBarController` из [Урок 11: UI](../Intermediate/Lesson11_UI.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя анимационная студия!  

**Предупреждение**: Анимации требуют точной настройки **Animator Controller** и параметров. Сохраняй код (**Ctrl+S**) перед тестом, иначе анимации не запустятся! Если герой или враги не анимируются, проверь связи в **Animator** и настройки триггеров. Врубай Play Mode и оживи арену!

Готов снять блокбастер? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Анимации?**
**Анимации** в Unity управляются через **Animator Component** и **Animator Controller**, которые задают, как объекты двигаются, атакуют или уничтожаются.  
- **Animator Controller**: "Режиссёр", который управляет переходами между анимациями.  
- **Parameters**: Условия (например, скорость или триггер), запускающие анимации.  
- **Animation Clips**: Файлы, содержащие кадры движения (например, ходьба, взрыв).  

В твоём шутере анимации сделают движение героя плавным, а уничтожение врагов — зрелищным!

*Ссылка на документацию*: [Animator](https://docs.unity3d.com/Manual/class-Animator.html).

## 🔄 **Зачем это Нужно?**
Анимации оживляют игру, делая её визуально привлекательной. В этом квесте ты:  
- Создашь анимацию движения для героя.  
- Добавишь эффект уничтожения врагов.  
- Сделаешь бой в твоём шутере кинематографичным!

**Почему это круто?**  
- **Зрелищность**: Анимации делают игру динамичной.  
- **Погружение**: Плавные движения и эффекты усиливают эмоции.  
- **Для шутера**: Это ключ к эпичным сражениям и крутым эффектам!

**Типичные ошибки новичков**:  
- Забыл привязать **Animator Controller** к объекту.  
- Неправильные параметры или переходы в **Animator**.  
- Отсутствует анимация в префабе или сцене.  

## ⚙️ **Квест: Оживи арену анимациями**

### Уровень 1: Анимация движения героя  
1. **Создай анимацию для игрока:**  
   - В **Project** создай папку `Animations`.  
   - Щёлкни правой кнопкой > **Create > Animator Controller**, назови `PlayerAnimator`.  
   - Открой `PlayerAnimator` в окне **Animator**.  

2. **Создай клип анимации движения:**  
   - В **Project** создай **Animation**, назови `PlayerWalk`.  
   - Выбери `Player` в **Hierarchy**, открой окно **Animation**, выбери `PlayerWalk`.  
   - В **Animation** добавь свойство `Position.y`, создай два ключевых кадра:  
     - Время 0: Position.y = 0.  
     - Время 0.5: Position.y = 0.2.  
   - Включите зацикливание (Loop Time) в настройках `PlayerWalk`.  

3. **Настрой Animator:**  
   - В **Animator** перетащи `PlayerWalk` как новый штатный переход.  
   - Добавь параметр `Speed` (тип Float) в **Animator > Parameters**.  
   - Создай переход от **Entry** к `PlayerWalk`:  
     - Условие: `Speed > 0.01`.  
   - Создай переход от `PlayerWalk` к **Empty**:  
     - Условие: `Speed < 0.01`.  

4. **Обнови `PlayerController`:**  
   - Добавь переменную для анимации:  
     ```csharp
     private Animator animator; // Аниматор
     ```
   - В `Start`:  
     ```csharp
     animator = GetComponent<Animator>();
     ```
   - В `Update` добавь управление анимацией:  
     ```csharp
     float speed = movement.magnitude;
     animator.SetFloat("Speed", speed);
     ```

5. **Полный код `PlayerController`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;
   using TMPro;

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
       [SerializeField] private TextMeshProUGUI scoreText;
       [SerializeField] private UnityEngine.UI.Slider healthBar;
       [SerializeField] private int scorePerHit = 50;
       private int currentHealth;
       private int currentAmmo;
       private int totalScore = 0;
       private bool canShoot = true;
       private Camera mainCamera;
       private Animator animator;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
           animator = GetComponent<Animator>();
           scoreText.text = "Очки: " + totalScore;
           healthBar.maxValue = maxHealth;
           healthBar.value = currentHealth;
           Debug.Log(playerName + " готов к битве! Здоровье: " + currentHealth + ", Скорость: " + moveSpeed);
           TakeDamage(60);
       }

       void Update()
       {
           float moveX = Input.GetAxis("Horizontal");
           float moveY = Input.GetAxis("Vertical");
           Vector3 movement = new Vector3(moveX, 0, moveY);
           transform.Translate(movement * moveSpeed * Time.deltaTime);
           float speed = movement.magnitude;
           animator.SetFloat("Speed", speed);

           if (currentHealth < 50)
           {
               moveSpeed = 2.0f;
               Debug.Log(playerName + " ранен и замедлен! Скорость: " + moveSpeed);
           }
           else
           {
               moveSpeed = 5.0f;
           }

           if (Input.GetKeyDown(KeyCode.Space) && canShoot && currentAmmo >= bulletsPerShot)
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
           healthBar.value = currentHealth;
           Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
           if (damage < 0)
           {
               totalScore += 10;
               scoreText.text = "Очки: " + totalScore;
           }
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
               if (hit.collider != null && (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss")))
               {
                   totalScore += scorePerHit;
                   scoreText.text = "Очки: " + totalScore;
                   Debug.Log("Прицел нацелен на " + hit.collider.name + "!");
               }
           }
           currentAmmo -= bulletsPerShot;
           Debug.Log("Осталось патронов: " + currentAmmo);
           yield return new WaitForSeconds(reloadTime);
           canShoot = true;
       }
   }
   ```

6. **Настрой и протестируй:**  
   - Добавь **Animator** к `Player` в **Hierarchy**, перетащи `PlayerAnimator` в поле `Controller`.  
   - Нажми **Play**, двигай героя (WASD) — он "прыгает" при движении благодаря анимации!  

### Уровень 2: Анимация уничтожения врага  
1. **Создай анимацию для врага:**  
   - В **Project** создай **Animation**, назови `EnemyDeath`.  
   - Выбери `Enemy.prefab`, открой окно **Animation**, выбери `EnemyDeath`.  
   - Добавь свойство `Scale`:  
     - Время 0: Scale = (1, 1, 1).  
     - Время 0.5: Scale = (0.1, 0.1, 0.1).  

2. **Настрой Animator для врага:**  
   - Создай **Animator Controller**, назови `EnemyAnimator`.  
   - Открой `EnemyAnimator`, добавь `EnemyDeath` как новый штатный переход.  
   - Добавь параметр `Die` (тип Trigger).  
   - Создай переход от **Any State** к `EnemyDeath`:  
     - Условие: триггер `Die`.  

3. **Обнови `EnemyStats`:**  
   - Добавь анимацию уничтожения:  
     ```csharp
     private Animator animator;

     void Start()
     {
         currentHealth = maxHealth;
         animator = GetComponent<Animator>();
     }

     public void TakeDamage(int damage)
     {
         currentHealth -= damage;
         Debug.Log(gameObject.name + " получил урон! Осталось здоровья: " + currentHealth);
         if (currentHealth <= 0)
         {
             animator.SetTrigger("Die");
             Debug.Log(gameObject.name + " уничтожен!");
             Destroy(gameObject, 0.5f); // Задержка для анимации
         }
     }
     ```

4. **Полный код `EnemyStats`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemyStats : MonoBehaviour
   {
       [SerializeField] private int maxHealth = 100;
       private int currentHealth;
       private Animator animator;

       void Start()
       {
           currentHealth = maxHealth;
           animator = GetComponent<Animator>();
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log(gameObject.name + " получил урон! Осталось здоровья: " + currentHealth);
           if (currentHealth <= 0)
           {
               animator.SetTrigger("Die");
               Debug.Log(gameObject.name + " уничтожен!");
               Destroy(gameObject, 0.5f);
           }
       }
   }
   ```

5. **Настрой и протестируй:**  
   - Открой `Enemy.prefab`, добавь **Animator**, перетащи `EnemyAnimator` в поле `Controller`.  
   - Нажми **Play**, стреляй по врагам — они уменьшаются перед уничтожением!  

### Уровень 3: Эффект попадания пули  
1. **Создай эффект попадания:**  
   - В **Hierarchy** создай **3D Object > Cube**, назови `HitEffect`, масштаб (x=0.2, y=0.2, z=0.2).  
   - Создай материал `HitEffectMat` (цвет жёлтый), прикрепи к `HitEffect`.  
   - Сделай префаб: щёлкни правой кнопкой > **Create Prefab**, сохрани как `Assets/Prefabs/HitEffect.prefab`.  

2. **Обнови `BulletController`:**  
   - Добавь эффект попадания:  
     ```csharp
     [SerializeField] private GameObject hitEffectPrefab; // Префаб эффекта попадания

     void Update()
     {
         Ray ray = new Ray(transform.position, transform.GetComponent<Rigidbody>().velocity.normalized);
         RaycastHit hit;
         if (Physics.Raycast(ray, out hit, 0.5f, LayerMask.GetMask("EnemyLayer", "BossLayer")))
         {
             if (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss"))
             {
                 hit.collider.GetComponent<EnemyStats>().TakeDamage(damage);
                 Instantiate(hitEffectPrefab, hit.point, Quaternion.identity);
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
             Instantiate(hitEffectPrefab, transform.position, Quaternion.identity);
             Debug.Log("Пуля попала в " + other.name + "!");
             Destroy(gameObject);
         }
     }
     ```

3. **Полный код `BulletController`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class BulletController : MonoBehaviour
   {
       [SerializeField] private int damage = 20;
       [SerializeField] private GameObject hitEffectPrefab;

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
                   Instantiate(hitEffectPrefab, hit.point, Quaternion.identity);
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
               Instantiate(hitEffectPrefab, transform.position, Quaternion.identity);
               Debug.Log("Пуля попала в " + other.name + "!");
               Destroy(gameObject);
           }
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Открой `Bullet.prefab`, перетащи `HitEffect.prefab` в поле `Hit Effect Prefab`.  
   - Нажми **Play**, стреляй по врагам — жёлтые кубики появляются при попадании!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для `Animator` и `Hit Effect Prefab`.  
- В **Scene/Game**: Герой анимируется при движении, враги "взрываются", эффекты появляются при попадании.  
- В **Console**: Сообщения о попаданиях и уничтожении.  

## 💡 **Квесты: Прокачай анимации!**
1. **Базовый квест**:  
   - В `PlayerWalk` увеличь `Position.y` до 0.4 на втором кадре.  
   - Проверь: герой "прыгает" выше при движении!  

2. **Квест на врагов**:  
   - В `EnemyDeath` добавь вращение (Rotation.z: 0 → 360).  
   - Проверь: враги вращаются перед уничтожением!  

3. **Квест на эффект**:  
   - Измени `HitEffectMat` на красный цвет.  
   - Проверь: эффекты попадания стали красными!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Создай анимацию стрельбы для героя:  
     - Создай **Animation** `PlayerShoot`, добавь свойство `Scale.x` (0 → 1.2 за 0.2 сек).  
     - В `PlayerAnimator` добавь триггер `Shoot`, переход от **Any State** к `PlayerShoot`.  
     - В `PlayerController` в `ShootWithReload` добавь:  
       ```csharp
       animator.SetTrigger("Shoot");
       ```  
     - Проверь: герой "вздрагивает" при стрельбе!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если анимации не работают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Привязан ли `Animator Controller`?  
  - Правильные ли параметры в **Animator**?  
- **Хочешь эпичности?** В `EnemyStats` добавь:  
  ```csharp
  Debug.Log("Анимация смерти запущена для " + gameObject.name);
  ```  
  - Увидишь запуск анимации в **Console**!  
- **Анимации не играют?** Проверь, что `Animation Clips` привязаны и параметры настроены.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Анимации делают игру живой и зрелищной, усиливая погружение и эмоции. Это ключ к эпичному визуальному опыту!

## Заключение: Анимации — Твой Кинематограф Боя! 💻
Ты освоил анимации, оживив героя и врагов. Твой шутер стал зрелищным! Следующий шаг — частицы для взрывов и эффектов. Продолжай, режиссёр кода!

**Что Далее?**  
- Перейди к [Частицы — Взрывы и Эффекты](../Intermediate/Lesson13_Particles.md) — добавь искры.  
- Вопросы: [Unity Learn: Animations](https://learn.unity.com/tutorial/animations).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
