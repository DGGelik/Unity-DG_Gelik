
# 🖥️ Урок 11: UI — Показывай Очки и Здоровье!

Привет, юный дизайнер арены! 💻 Добро пожаловать на пятый уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером интерфейса**, освоив **UI** (пользовательский интерфейс) в Unity, чтобы отображать здоровье и очки игрока в твоём топ-даун шутере (*UnityTopDownShooterQuest*). UI — это как панель управления: показывает игроку важные данные. Ты создашь шкалу здоровья и счётчик очков! Готов сделать игру информативной? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus`, `CrosshairController` из [Урок 10: Raycast](../Intermediate/Lesson10_Raycast.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя студия дизайна!  

**Предупреждение**: UI требует точной настройки Canvas и компонентов. Сохраняй код (**Ctrl+S**) перед тестом, иначе интерфейс не обновится! Если текст или шкала не видны, проверь настройки Canvas и ссылок в скриптах. Врубай Play Mode и покажи игроку статистику!

Готов создать интерфейс? Погнали по уровням квеста! 🚀

## 🎯 **Что такое UI?**
**UI** (User Interface) в Unity — это элементы, которые показывают игроку информацию: текст, шкалы, кнопки. Основные компоненты:  
- **Canvas**: Контейнер для UI (должен быть в сцене).  
- **Text (TextMeshPro)**: Для отображения текста (очки, здоровье).  
- **Slider**: Для шкалы (например, здоровья).  

В твоём шутере UI покажет здоровье героя и набранные очки, чтобы игрок знал своё состояние!

*Ссылка на документацию*: [UI System](https://docs.unity3d.com/Manual/UISystem.html).

## 🔄 **Зачем это Нужно?**
UI делает игру понятной: игрок видит своё здоровье и прогресс. В этом квесте ты:  
- Добавишь текст для отображения очков.  
- Создашь шкалу здоровья.  
- Сделаешь интерфейс, который обновляется в реальном времени!

**Почему это круто?**  
- **Информативность**: Игрок всегда знает свой статус.  
- **Профессионализм**: UI делает игру похожей на настоящую!  
- **Для шутера**: Это основа для отображения статистики и прогресса!

**Типичные ошибки новичков**:  
- Забыл настроить `Canvas` (режим `Scale With Screen Size`).  
- Неправильные ссылки на UI-компоненты в скриптах.  
- Не обновляется UI (забыл `using TMPro`).  

## ⚙️ **Квест: Создай интерфейс для арены**

### Уровень 1: Настрой UI для очков  
1. **Создай Canvas и Text:**  
   - В **Hierarchy** щёлкни правой кнопкой > **UI > Canvas**, назови `GameUI`.  
   - Убедись, что в **Inspector** для `Canvas` установлено `Scale With Screen Size`.  
   - В `GameUI` создай **UI > Text - TextMeshPro**, назови `ScoreText`.  
   - Установи позицию `ScoreText` (x=100, y=-50), текст: "Очки: 0", шрифт: 24, цвет: белый.  

2. **Обнови `PlayerController`:**  
   - Добавь переменные для UI и очков:  
     ```csharp
     [SerializeField] private TMPro.TextMeshProUGUI scoreText; // Текст очков
     [SerializeField] private int scorePerHit = 50; // Очки за попадание
     private int totalScore = 0; // Общий счёт
     ```

3. **Добавь обновление очков:**  
   - В `Start`:  
     ```csharp
     scoreText.text = "Очки: " + totalScore;
     ```
   - В `TakeDamage` добавь очки за выживание:  
     ```csharp
     if (damage < 0) // Если лечение
     {
         totalScore += 10;
         scoreText.text = "Очки: " + totalScore;
     }
     ```
   - В `ShootWithReload` добавь очки за попадание:  
     ```csharp
     if (hit.collider != null && (hit.collider.CompareTag("Enemy") || hit.collider.CompareTag("Boss")))
     {
         totalScore += scorePerHit;
         scoreText.text = "Очки: " + totalScore;
         Debug.Log("Прицел нацелен на " + hit.collider.name + "!");
     }
     ```

4. **Полный код `PlayerController`:**  
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
       [SerializeField] private int scorePerHit = 50;
       private int currentHealth;
       private int currentAmmo;
       private int totalScore = 0;
       private bool canShoot = true;
       private Camera mainCamera;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
           scoreText.text = "Очки: " + totalScore;
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

5. **Настрой и протестируй:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** выбери `Player`, перетащи `ScoreText` в поле `Score Text` в **Inspector**.  
   - Нажми **Play**, стреляй по врагам, собирай бонусы — очки обновляются на экране!  

### Уровень 2: Шкала здоровья  
1. **Создай Slider:**  
   - В `GameUI` создай **UI > Slider**, назови `HealthBar`.  
   - Установи позицию (x=-100, y=-50), масштаб (x=2, y=2, z=1).  
   - В **Inspector** для `Slider` установи `Max Value` = 100, `Value` = 100.  

2. **Обнови `PlayerController`:**  
   - Добавь переменную для шкалы:  
     ```csharp
     [SerializeField] private UnityEngine.UI.Slider healthBar; // Шкала здоровья
     ```

3. **Обнови здоровье в коде:**  
   - В `Start`:  
     ```csharp
     healthBar.maxValue = maxHealth;
     healthBar.value = currentHealth;
     ```
   - В `TakeDamage`:  
     ```csharp
     healthBar.value = currentHealth;
     ```

4. **Полный код `PlayerController`:**  
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

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
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

5. **Настрой и протестируй:**  
   - Сохрани код (**Ctrl+S**).  
   - В **Hierarchy** выбери `Player`, перетащи `HealthBar` в поле `Health Bar` и `ScoreText` в поле `Score Text` в **Inspector**.  
   - Нажми **Play**, стреляй по врагам, собирай бонусы — шкала здоровья и очки обновляются!  

### Уровень 3: Улучшение UI с цветом шкалы  
1. **Добавь цвет шкалы здоровья:**  
   - В **Hierarchy** найди `HealthBar > Fill Area > Fill`, создай материал (**Project > Create > Material**, назови `HealthBarFillMat`, цвет зелёный).  
   - Прикрепи материал к `Fill`.  

2. **Создай скрипт для динамического цвета:**  
   - В **Project** создай **C# Script**, назови `HealthBarController`.  
   - Добавь код:  
     ```csharp
     using UnityEngine;
     using UnityEngine.UI;

     public class HealthBarController : MonoBehaviour
     {
         [SerializeField] private Slider healthBar;
         [SerializeField] private Image fillImage;
         [SerializeField] private Color fullHealthColor = Color.green;
         [SerializeField] private Color lowHealthColor = Color.red;

         void Update()
         {
             float healthPercentage = healthBar.value / healthBar.maxValue;
             fillImage.color = Color.Lerp(lowHealthColor, fullHealthColor, healthPercentage);
         }
     }
     ```

3. **Настрой и протестируй:**  
   - Прикрепи `HealthBarController` к `HealthBar`.  
   - В **Inspector** перетащи `HealthBar` в поле `Health Bar`, а `Fill` в поле `Fill Image`.  
   - Нажми **Play**, получай урон или бонусы — шкала меняет цвет от зелёного (полное здоровье) к красному (низкое здоровье)!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Score Text` и `Health Bar` для `Player`, настройки цвета для `HealthBarController`.  
- В **Scene/Game**: Очки отображаются вверху, шкала здоровья слева, цвет шкалы меняется.  
- В **Console**: Сообщения о выстрелах, попаданиях и здоровье.  

## 💡 **Квесты: Прокачай интерфейс!**
1. **Базовый квест**:  
   - В **Inspector** для `ScoreText` установи шрифт 32, цвет жёлтый.  
   - Проверь: текст очков стал ярче и крупнее!  

2. **Квест на шкалу**:  
   - В **Inspector** для `HealthBar` установи `Max Value` = 200, для `Player` установи `Max Health` = 200.  
   - Проверь: шкала и здоровье игрока увеличены!  

3. **Квест на очки**:  
   - В `PlayerController` увеличь `Score Per Hit` до 100 в **Inspector**.  
   - Проверь: больше очков за попадания!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - В `PlayerController` добавь отображение патронов:  
     - Создай **UI > Text - TextMeshPro**, назови `AmmoText`, позиция (x=100, y=-100), текст: "Патроны: 0".  
     - В `PlayerController` добавь:  
       ```csharp
       [SerializeField] private TextMeshProUGUI ammoText;
       ```
     - В `Start`:  
       ```csharp
       ammoText.text = "Патроны: " + currentAmmo;
       ```
     - В `ShootWithReload`:  
       ```csharp
       ammoText.text = "Патроны: " + currentAmmo;
       ```
     - Проверь: текст показывает текущее количество патронов!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если UI не обновляется:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь ссылки на `ScoreText` и `HealthBar` в **Inspector**.  
  - Добавлен ли `using TMPro`?  
- **Хочешь эпичности?** В `HealthBarController` добавь:  
  ```csharp
  Debug.Log("Цвет шкалы здоровья: " + fillImage.color);
  ```  
  - Увидишь текущий цвет шкалы в **Console**!  
- **UI не видно?** Проверь, что `Canvas` настроен на `Scale With Screen Size`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
UI делает игру понятной и профессиональной, позволяя игроку следить за здоровьем и прогрессом. Это ключ к вовлечённости!

## Заключение: UI — Твоя Панель Управления! 💻
Ты освоил UI, добавив шкалу здоровья и счётчик очков. Твой шутер стал информативным! Следующий шаг — анимации для эпичных эффектов. Продолжай, дизайнер кода!

**Что Далее?**  
- Перейди к [Анимации — Оживи Бой](../Intermediate/Lesson12_Animations.md) — добавь эффекты.  
- Вопросы: [Unity Learn: UI](https://learn.unity.com/tutorial/ui-components).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
