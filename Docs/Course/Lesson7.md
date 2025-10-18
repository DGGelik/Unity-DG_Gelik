# 💥 Урок 7: Физика Перестрелки — Пули, Столкновения и Урон

Привет, физик-артиллерист! 💥 В этом уроке мы добавим **реальную физику стрельбы**: пули как летящие объекты (с Rigidbody), обнаружение столкновений (OnCollisionEnter) и систему урона (снижение HP врагов и героя, обновление HUD). Твой топ-даун шутер оживёт — пули полетят, враги взорвутся! Интегрируем с анимациями из Урока 6. Время: 30–45 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с анимациями из Урока 6.  
- Скрипты PlayerMovement и UIHealthManager из предыдущих уроков.  

**Предупреждение**: Пули могут "протыкать" объекты — настрой Colliders правильно. Если ошибки в Console — читай их внимательно!  

Готов запустить залп? По шагам, как по траекториям! 📝

## Шаг 1: Создай Prefab Пули — Летящий Проект с Физикой 🔫
Пули — это prefab с Rigidbody для полёта.  

1. В **Hierarchy** правой кнопкой → **3D Object > Sphere**. Назови "Bullet".  
2. В Inspector > Transform: Scale X=0.2, Y=0.2, Z=0.2 (маленькая пуля).  
3. Добавь физику: **Add Component > Physics > Rigidbody**.  
   - Use Gravity: Off (пули летят прямо).  
   - Drag: 0.5 (замедление в воздухе).  
4. Добавь коллайдер: **Add Component > Physics > Sphere Collider**. Is Trigger: On (для обнаружения без толчка).  
5. Создай материал: В Project > Materials > Create > Material "BulletMat", цвет жёлтый, перетащи на Bullet.  
6. Сделай prefab: Перетащи Bullet в Project > Prefabs. Удали из Hierarchy.  

**Готово!** Пуля готова к запуску.  
*Ссылка на помощь*: [Создание Prefabs](https://docs.unity3d.com/Manual/Prefabs.html).  

## Шаг 2: Реализуй Стрельбу в Скрипте — Инстанс Пули и Направление 🛫
Теперь скрипт создаст пулю по клику и даст скорость.  

1. Открой скрипт "PlayerMovement". Добавь в класс:  
   ```
   public GameObject bulletPrefab;  // Prefab пули
   public Transform firePoint;      // Точка выстрела (child героя)
   public float bulletSpeed = 20f;  // Скорость пули
   ```  
2. В Start():  
   ```
   firePoint = transform.Find("FirePoint");  // Создай пустой GameObject как child героя, назови FirePoint
   ```  
3. В Update() для стрельбы (в if Input.GetMouseButtonDown(0)):  
   ```
   GameObject bullet = Instantiate(bulletPrefab, firePoint.position, firePoint.rotation);
   Rigidbody bulletRb = bullet.GetComponent<Rigidbody>();
   bulletRb.velocity = firePoint.forward * bulletSpeed;  // Полёт вперёд

   animator.SetTrigger("Shoot");
   ```  
4. Сохрани. В Inspector героя: Перетащи Bullet prefab в bulletPrefab, FirePoint в firePoint.  

**Готово!** Клик — и пули летят!  
*Ссылка на помощь*: [Instantiate и Rigidbody Velocity](https://docs.unity3d.com/ScriptReference/Object.Instantiate.html).  

## Шаг 3: Добавь Систему Урона — Столкновения и HP 👊
Скрипты для урона: пуля наносит урон при касании.  

1. Создай скрипт "BulletDamage": В Project > Create > C# Script "BulletDamage". Код:  
   ```
   using UnityEngine;

   public class BulletDamage : MonoBehaviour
   {
       public int damage = 10;  // Урон пули
       public float lifetime = 3f;  // Время жизни пули

       void Start()
       {
           Destroy(gameObject, lifetime);  // Самоуничтожение
       }

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Enemy"))  // Тег для врагов
           {
               EnemyHealth enemyHealth = other.GetComponent<EnemyHealth>();
               if (enemyHealth != null)
               {
                   enemyHealth.TakeDamage(damage);
               }
               Destroy(gameObject);  // Уничтожь пулю
           }
       }
   }
   ```  
2. Прикрепи к Bullet prefab: Открой prefab (двойной клик в Project), Add Component > BulletDamage.  
3. Создай скрипт "EnemyHealth" для врагов (из Урока 5): Код:  
   ```
   using UnityEngine;

   public class EnemyHealth : MonoBehaviour
   {
       public int maxHealth = 50;
       private int currentHealth;

       void Start()
       {
           currentHealth = maxHealth;
       }

       public void TakeDamage(int damage)
       {
           currentHealth -= damage;
           if (currentHealth <= 0)
           {
               Die();
           }
       }

       void Die()
       {
           // Взрыв или Destroy
           Destroy(gameObject);
       }
   }
   ```  
4. Прикрепи EnemyHealth к EnemyPrefab. Добавь Tag "Enemy" (в Inspector > Tag > Add Tag).  

**Готово!** Пули наносят урон врагам.  
*Ссылка на помощь*: [OnTriggerEnter и Tags](https://docs.unity3d.com/Manual/Colliders.html).  

## Шаг 4: Сохрани и Протестируй — Полная Перестрелка ▶️
Добавь врага и проверим физику.  

1. Сохрани сцену и скрипты: **File > Save** (Ctrl+S).  
2. В Hierarchy перетащи EnemyPrefab на арену (Position случайно).  
3. Кликни **Play**.  
4. Двигай героя, кликай — пули летят, касаются врага? Он уничтожается?  
5. Кликни **Stop**. Если пули не попадают — проверь Colliders и Tags.  

**Ура!** Физика стрельбы работает — урон летит!  
*Ссылка на помощь*: [Физика в Unity](https://docs.unity3d.com/Manual/PhysicsSection.html).  

## Что Далее? 🚀
- Перейди к [Уроку 8: Эффекты Хаоса — Частицы, Звуки Выстрелов и Префабы Врагов](./Lesson8.md) — добавим звук и спавн!  
- Вопросы: [Unity Learn: Physics and Collisions](https://learn.unity.com/tutorial/physics).  

Твои пули — молнии! Продолжай, снайпер. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*