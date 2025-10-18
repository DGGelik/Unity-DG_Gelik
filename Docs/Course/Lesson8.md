# 🌈 Урок 8: Эффекты Хаоса — Частицы, Звуки Выстрелов и Префабы Врагов

Привет, мастер спецэффектов! 🌈 В этом уроке мы добавим **хаос в перестрелку**: расширим частицы для вспышек выстрелов и взрывов, звуки (выстрелы, попадания) и систему префабов врагов с простым спавнером (волны ботов). Твой топ-даун шутер заискрится — пули зашумят, взрывы осветят арену! Свяжем с физикой из Урока 7. Время: 30–45 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с физикой из Урока 7.  
- Бесплатные звуки/частицы из Asset Store.  

**Предупреждение**: Звуки могут быть громкими — настрой громкость в Audio Mixer. Если спавн не работает — проверь prefab в скрипте!  

Готов взорвать арену? По шагам, как по спецэффектам! 📝

## Шаг 1: Расширь Частицы — Вспышки Выстрелов и Взрывы 💥
Частицы добавят визуала к пулям и попаданиям.  

1. В Project создай папку Effects (правой кнопкой на Assets > Create > Folder).  
2. Правой кнопкой в Hierarchy → **Effects > Particle System**. Назови "MuzzleFlash" (для дула).  
3. В Inspector > Particle System:  
   - Duration: 0.1 сек (короткая вспышка).  
   - Start Lifetime: 0.5.  
   - Emission: Rate over Time=50 (много частиц).  
   - Shape: Cone (угол 10°).  
   - Renderer: Material — жёлтый (как BulletMat из Урока 7).  
4. Создай prefab: Перетащи MuzzleFlash в Project > Effects. Удали из Hierarchy.  
5. В скрипте PlayerMovement (в if стрельбы):  
   ```
   GameObject flash = Instantiate(muzzleFlashPrefab, firePoint.position, firePoint.rotation);
   Destroy(flash, 0.1f);  // Короткий эффект
   ```  
6. Для взрыва: Дублируй Particle System, назови "Explosion", Emission=200, Color=Оранжевый. Сделай prefab.  

**Готово!** Эффекты готовы к запуску.  
*Ссылка на помощь*: [Particle System Effects](https://docs.unity3d.com/Manual/ParticleSystems.html).  

## Шаг 2: Добавь Звуки Выстрелов и Попаданий — Аудио-Фидбек 🔊
Звуки сделают бой immersive.  

1. В Asset Store ищи "free gun sounds" — скачай pack (например, "Weapon Sounds Free"). Импортируй.  
2. В Project создай папку Audio. Перетащи .wav файлы (shot.wav, hit.wav).  
3. На герое: Выбери героя в Hierarchy → **Add Component > Audio > Audio Source**.  
   - AudioClip: Перетащи shot.wav.  
   - Play On Awake: Off.  
   - Volume: 0.5.  
4. В скрипте PlayerMovement (в if стрельбы):  
   ```
   GetComponent<AudioSource>().PlayOneShot(shotClip);  // shotClip — public AudioClip в классе
   ```  
5. В скрипте BulletDamage (в OnTriggerEnter):  
   ```
   AudioSource.PlayClipAtPoint(hitClip, transform.position);  // Глобальный звук попадания
   ```  
   (Добавь public AudioClip hitClip в класс).  
6. Сохрани скрипты.  

**Готово!** Выстрелы гремят, попадания — ба-бах!  
*Ссылка на помощь*: [Audio Source](https://docs.unity3d.com/Manual/AudioSources.html).  

## Шаг 3: Создай Префабы Врагов и Спавнер — Волны Хаоса 👾
Префабы — шаблоны для множественного спавна врагов.  

1. Открой EnemyPrefab (двойной клик в Project). Убедись: EnemyAI, EnemyHealth, Collider (Box Collider).  
2. Добавь эффекты: Child GameObject с Explosion prefab, активируй в Die() скрипта EnemyHealth:  
   ```
   Instantiate(explosionPrefab, transform.position, Quaternion.identity);
   ```  
3. Создай скрипт "EnemySpawner": Create > C# Script "EnemySpawner". Код:  
   ```
   using UnityEngine;
   using System.Collections;

   public class EnemySpawner : MonoBehaviour
   {
       public GameObject enemyPrefab;
       public Transform[] spawnPoints;  // Точки спавна (создай пустые GameObjects)
       public int waveSize = 3;
       public float spawnDelay = 2f;

       void Start()
       {
           StartCoroutine(SpawnWave());
       }

       IEnumerator SpawnWave()
       {
           for (int i = 0; i < waveSize; i++)
           {
               int randomPoint = Random.Range(0, spawnPoints.Length);
               Instantiate(enemyPrefab, spawnPoints[randomPoint].position, Quaternion.identity);
               yield return new WaitForSeconds(spawnDelay);
           }
       }
   }
   ```  
4. В Hierarchy: Создай пустой "Spawner", Add Component > EnemySpawner. Перетащи enemyPrefab, создай 4 пустых SpawnPoint по краям арены.  

**Готово!** Враги спавнятся волнами.  
*Ссылка на помощь*: [Coroutine для Спавна](https://docs.unity3d.com/Manual/Coroutines.html).  

## Шаг 4: Сохрани и Протестируй — Хаос на Арена ▶️
Запустим полный эффект.  

1. Сохрани сцену и скрипты: **File > Save** (Ctrl+S).  
2. Кликни **Play**.  
3. Двигай героя, стреляй — вспышки, звуки, взрывы? Враги спавнятся и умирают?  
4. Кликни **Stop**. Если звуки не играют — проверь AudioClip в Inspector.  

**Ура!** Эффекты хаоса — шутер в огне!  
*Ссылка на помощь*: [Интеграция Аудио и Частиц](https://docs.unity3d.com/Manual/AudioSources.html).  

## Что Далее? 🚀
- Перейди к [Уроку 9: Тестирование Битвы — Сборка, Баги и Баланс Огня](./Lesson9.md) — соберём игру!  
- Вопросы: [Unity Learn: Particles and Audio](https://learn.unity.com/tutorial/particles-and-audio).  

Твой хаос — симфония! Продолжай, пиротехник. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*