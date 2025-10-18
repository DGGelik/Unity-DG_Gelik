
# 🔊 Урок 13: Звук — Добавь Выстрелы и Музыку!

Привет, юный звукорежиссёр арены! 💻 Добро пожаловать на седьмой уровень *Части 3* твоего квеста *UnityCSQuest*! Сегодня ты станешь **мастером звуков**, освоив **Audio System** в Unity, чтобы добавить звуки выстрелов, взрывов и фоновую музыку в твой топ-даун шутер (*UnityTopDownShooterQuest*). Звуки делают игру живой: от грохота выстрелов до эпичной музыки сражений. Ты добавишь звуковые эффекты для пуль, врагов и бонусов, а также фоновую мелодию! Готов задать ритм боя? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `EnemyStats`, `EnemySpawner`, `BulletController`, `HealthBonus`, `CrosshairController`, `HealthBarController` из [Урок 12: Анимации](../Intermediate/Lesson12_Animations.md).  
- Префабы `Bullet`, `Enemy`, `HealthBonus`, `HitEffect` в `Assets/Prefabs`.  
- Аудиофайлы (рекомендуется .wav или .mp3): выстрелы, взрывы, бонусы, фоновая музыка (скачай бесплатные звуки, например, с [Freesound.org](https://freesound.org)).  
- Папка Scripts в Assets — твоя звуковая студия!  

**Предупреждение**: Audio System требует настройки **Audio Source** и привязки аудиофайлов. Сохраняй код (**Ctrl+S**) перед тестом, иначе звуки не заиграют! Если звуки не слышны, проверь громкость в **Audio Source** и правильность привязки файлов. Врубай Play Mode и устрой звуковую бурю!

Готов зазвучать? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Audio System?**
**Audio System** в Unity отвечает за воспроизведение звуков и музыки. Основные компоненты:  
- **Audio Source**: Компонент, проигрывающий аудиофайлы.  
- **Audio Clip**: Файл звука (.wav, .mp3).  
- **Audio Listener**: "Уши" сцены, обычно на камере.  

В твоём шутере звуки добавят реализма: выстрелы загрохочут, враги взорвутся с треском, а музыка создаст атмосферу!

*Ссылка на документацию*: [Audio System](https://docs.unity3d.com/Manual/Audio.html).

## 🔄 **Зачем это Нужно?**
Звуки усиливают погружение, делая каждый выстрел и взрыв ощутимыми. В этом квесте ты:  
- Добавишь звук выстрелов для героя.  
- Создашь звуки взрывов для врагов и бонусов.  
- Включишь фоновую музыку для арены.  
- Сделаешь твой шутер атмосферным и эпичным!

**Почему это круто?**  
- **Реализм**: Звуки делают бой живым.  
- **Атмосфера**: Музыка задаёт настроение сражений.  
- **Для шутера**: Это ключ к полному погружению в бой!

**Типичные ошибки новичков**:  
- Забыл добавить **Audio Source** к объекту.  
- Неправильная привязка **Audio Clip** в **Inspector**.  
- Нет **Audio Listener** в сцене (обычно на `Main Camera`).  

## ⚙️ **Квест: Заряди арену звуками**

### Уровень 1: Звук выстрелов героя  
1. **Подготовь аудиофайлы:**  
   - Скачай или создай звуковой файл выстрела (`shot.wav`) и помести в `Assets/Audio`.  
   - Рекомендуемые настройки: короткий звук (0.2–0.5 сек), формат .wav.  

2. **Обнови `PlayerController`:**  
   - Добавь переменные для звука:  
     ```csharp
     [SerializeField] private AudioClip shotSound; // Звук выстрела
     private AudioSource audioSource; // Источник звука
     ```
   - В `Start`:  
     ```csharp
     audioSource = GetComponent<AudioSource>();
     ```
   - В `ShootWithReload` добавь звук:  
     ```csharp
     audioSource.PlayOneShot(shotSound);
     ```

3. **Полный код `PlayerController`:**  
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
       [SerializeField] private AudioClip shotSound;
       private int currentHealth;
       private int currentAmmo;
       private int totalScore = 0;
       private bool canShoot = true;
       private Camera mainCamera;
       private Animator animator;
       private AudioSource audioSource;

       void Start()
       {
           currentHealth = maxHealth;
           currentAmmo = maxAmmo;
           mainCamera = Camera.main;
           animator = GetComponent<Animator>();
           audioSource = GetComponent<AudioSource>();
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
           audioSource.PlayOneShot(shotSound);
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

4. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Player`, добавь **Audio Source** (**Add Component**), сними галочку с `Play On Awake`.  
   - Перетащи `shot.wav` в поле `Shot Sound` в **Inspector**.  
   - Нажми **Play**, стреляй (**Space**) — слышен звук выстрела!  

### Уровень 2: Звук взрыва врагов  
1. **Подготовь аудиофайл:**  
   - Скачай или создай звук взрыва (`explosion.wav`) и помести в `Assets/Audio`.  
   - Рекомендуемые настройки: короткий звук (0.5–1 сек).  

2. **Обнови `EnemyStats`:**  
   - Добавь переменные для звука:  
     ```csharp
     [SerializeField] private AudioClip explosionSound; // Звук взрыва
     ```

   - В `TakeDamage` добавь звук:  
     ```csharp
     Instantiate(explosionPrefab, transform.position, Quaternion.identity);
     AudioSource.PlayClipAtPoint(explosionSound, transform.position);
     ```

3. **Полный код `EnemyStats`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class EnemyStats : MonoBehaviour
   {
       [SerializeField] private int maxHealth = 100;
       [SerializeField] private GameObject explosionPrefab;
       [SerializeField] private AudioClip explosionSound;
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
               Instantiate(explosionPrefab, transform.position, Quaternion.identity);
               AudioSource.PlayClipAtPoint(explosionSound, transform.position);
               Debug.Log(gameObject.name + " уничтожен с взрывом!");
               Destroy(gameObject, 0.5f);
           }
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Открой `Enemy.prefab`, перетащи `explosion.wav` в поле `Explosion Sound`.  
   - Нажми **Play**, стреляй по врагам — при уничтожении слышен звук взрыва!  

### Уровень 3: Фоновая музыка и звук бонусов  
1. **Добавь фоновую музыку:**  
   - Скачай или создай музыкальный трек (`battle_music.mp3`) и помести в `Assets/Audio`.  
   - В **Hierarchy** выбери `Main Camera`, добавь **Audio Source**.  
   - Настрой:  
     - **Audio Clip**: Перетащи `battle_music.mp3`.  
     - **Loop**: Включить.  
     - **Play On Awake**: Включить.  
     - **Volume**: 0.3 (для ненавязчивости).  

2. **Добавь звук бонусов:**  
   - Скачай или создай звук бонуса (`bonus.wav`) и помести в `Assets/Audio`.  
   - В `HealthBonus` добавь:  
     ```csharp
     [SerializeField] private AudioClip bonusSound; // Звук бонуса
     ```

   - В `OnTriggerEnter`:  
     ```csharp
     AudioSource.PlayClipAtPoint(bonusSound, transform.position);
     ```

3. **Полный код `HealthBonus`:**  
   ```csharp
   using System.Collections;
   using System.Collections.Generic;
   using UnityEngine;

   public class HealthBonus : MonoBehaviour
   {
       [SerializeField] private int healthBoost = 25;
       [SerializeField] private GameObject healEffectPrefab;
       [SerializeField] private AudioClip bonusSound;

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Player"))
           {
               other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
               Instantiate(healEffectPrefab, transform.position, Quaternion.identity);
               AudioSource.PlayClipAtPoint(bonusSound, transform.position);
               Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
               Destroy(gameObject);
           }
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Открой `HealthBonus.prefab`, перетащи `bonus.wav` в поле `Bonus Sound`.  
   - Нажми **Play**, собери бонус — слышен звук бонуса, а фоновая музыка играет!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для звуков (`Shot Sound`, `Explosion Sound`, `Bonus Sound`).  
- В **Scene/Game**: Звуки выстрелов, взрывов и бонусов, фоновая музыка.  
- В **Console**: Сообщения о попаданиях, взрывах и бонусах.  

## 💡 **Квесты: Прокачай звук!**
1. **Базовый квест**:  
   - В **Inspector** для `Player` увеличь громкость `Audio Source` до 0.5.  
   - Проверь: выстрелы стали громче!  

2. **Квест на взрывы**:  
   - Замени `explosion.wav` на другой звук (например, более громкий).  
   - Проверь: взрывы звучат иначе!  

3. **Квест на музыку**:  
   - В `Main Camera` уменьши `Volume` музыки до 0.1.  
   - Проверь: музыка тише, но всё ещё слышна!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь случайный тон выстрела:  
     - В `PlayerController` в `ShootWithReload`:  
       ```csharp
       audioSource.pitch = Random.Range(0.8f, 1.2f);
       audioSource.PlayOneShot(shotSound);
       audioSource.pitch = 1.0f; // Сброс тона
       ```  
     - Проверь: выстрелы звучат с разной высотой тона!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если звуки не играют:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Привязаны ли аудиофайлы в **Inspector**?  
  - Есть ли **Audio Listener** на `Main Camera`?  
- **Хочешь эпичности?** В `EnemyStats` добавь:  
  ```csharp
  Debug.Log("Звук взрыва на позиции: " + transform.position);
  ```  
  - Увидишь позиции звуков в **Console**!  
- **Звуки не слышны?** Проверь громкость в **Audio Source** и формат файлов (.wav или .mp3).  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Звуки делают игру эмоциональной и атмосферной, усиливая эффект от выстрелов, взрывов и наград. Это ключ к полному погружению!

## Заключение: Звук — Грохот Твоего Боя! 💻
Ты освоил звуки, добавив выстрелы, взрывы и музыку. Твой шутер стал настоящей симфонией боя! Следующий шаг — сохранение игры для прогресса игрока. Продолжай, звукорежиссёр кода!

**Что Далее?**  
- Перейди к [Сохранение — Запомни Прогресс Игрока](../Intermediate/Lesson14_Saving.md) — сохраняй очки.  
- Вопросы: [Unity Learn: Audio](https://learn.unity.com/tutorial/audio).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
