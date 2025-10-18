# 🏆 Урок 10: Финальный Шутер — Дораработка, Боссы и Портфолио-Атака

Привет, легенда геймдева! 🏆 Это финальный уровень — мы доработаем твой топ-даун шутер: добавим главного босса (мощный враг с фазами), финальную полировку (меню, конец игры, баланс) и соберём портфолио (видео, скрины, описание для GitHub или itch.io). Твоя игра готова к миру — от арены к славе! Время: 40–60 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с тестированием из Урока 9.  
- Инструменты: OBS Studio для записи видео (бесплатно).  

**Предупреждение**: Финальная сборка — не торопись, проверь все скрипты. Портфолио — публичное, убедись в оригинальности ассетов!  

Готов к апофеозу? По шагам, как по титрам! 📝

## Шаг 1: Доработка Игры — Меню, Конец и Баланс 🎬
Полировка: добавим главное меню и экран победы/поражения.  

1. Создай новую сцену: **File > New Scene**, сохрани как "MainMenu".  
2. Добавь UI: Правой кнопкой на Canvas > **UI > Button**. Назови "StartButton", текст "Start Game".  
3. Скрипт для кнопки: Create > C# Script "MenuManager". Код:  
   ```
   using UnityEngine;
   using UnityEngine.SceneManagement;

   public class MenuManager : MonoBehaviour
   {
       public void StartGame()
       {
           SceneManagement.LoadScene("Level1_Arena");
       }
   }
   ```  
   Прикрепи к Canvas, в OnClick кнопки перетащи MenuManager и выбери StartGame().  
4. Для конца игры: В скрипте UIHealthManager добавь: Если health <=0 — загрузи "GameOver" сцену (создай аналогично).  
5. Баланс: В EnemySpawner waveSize += Random.Range(1,3); bulletDamage = 12; протестируй и собери.  

**Готово!** Игра с меню — профессионально.  
*Ссылка на помощь*: [Scene Management](https://docs.unity3d.com/Manual/SceneManagement.html).  

## Шаг 2: Добавь Босса — Эпический Финальный Враг 👹
Босс — усиленный враг с фазами и атаками.  

1. Дублируй EnemyPrefab, назови "BossPrefab". В Inspector: Scale=2 (больше), maxHealth=200.  
2. Скрипт "BossAI": Create > C# Script "BossAI". Код:  
   ```
   using UnityEngine;

   public class BossAI : MonoBehaviour
   {
       public Transform player;
       public GameObject bossBulletPrefab;  // Пули босса
       public float speed = 3f;
       public float shootInterval = 2f;
       private float nextShoot;

       void Update()
       {
           // Движение к игроку
           Vector3 direction = (player.position - transform.position).normalized;
           transform.Translate(direction * speed * Time.deltaTime);

           // Стрельба фазами
           if (Time.time > nextShoot)
           {
               Instantiate(bossBulletPrefab, transform.position, Quaternion.identity);
               nextShoot = Time.time + shootInterval;
           }
       }
   }
   ```  
3. В EnemySpawner: На 10-й волне спавнь босса: if (waveNumber == 10) Instantiate(bossPrefab...);  
4. Добавь фазы: В BossAI if (health < 100) shootInterval = 1f; (свяжи с EnemyHealth).  

**Готово!** Босс — кульминация боя.  
*Ссылка на помощь*: [Расширенный AI](https://docs.unity3d.com/Manual/class-NavMeshAgent.html).  

## Шаг 3: Собери Портфолио — Видео, Скрины и Описание 📸
Портфолио — твоя визитка для школ/работ.  

1. Запиши геймплей: Установи OBS Studio (obsproject.com), запиши 1-мин видео: запуск, бой, босс.  
2. Сделай скрины: В Unity **Window > Analysis > Frame Debugger** или Print Screen в Play Mode (арена, HUD, взрывы).  
3. Описание: В Google Docs напиши: "MEGA Shooter — мой топ-даун 3D шутер на Unity. Фичи: WASD движение, мышь-стрельба, волны врагов, босс. Сделал за 10 уроков. Скачай: [ссылка на .exe или itch.io]."  
4. Загрузи на GitHub: Создай Releases с .exe, добавь README с видео (embed YouTube) и скринами.  

**Готово!** Портфолио готово к показу.  
*Ссылка на помощь*: [Портфолио в Unity](https://docs.unity3d.com/Manual/UnityManual.html#portfolios).  

## Шаг 4: Финальный Тест и Сборка — Эпический Релиз ▶️
Проверим весь эпос.  

1. Сохрани все сцены и скрипты: **File > Save All**.  
2. В Build Settings добавь все сцены (MainMenu, Level1_Arena).  
3. Build > Build (включи Development Build для логов).  
4. Запусти .exe: Полный цикл — меню, бой, босс, конец? Баланс ок?  
5. Загрузи на itch.io (itch.io) для шаринга.  

**Ура!** Шутер завершён — ты геймдев!  
*Ссылка на помощь*: [Финальная Сборка](https://docs.unity3d.com/Manual/PublishingBuilds.html).  

## Что Далее? 🌟
- Поздравляю! Курс окончен — поделись портфолио в соцсетях.  
- Вопросы: [Unity Learn: Advanced Polish](https://learn.unity.com/pathway/advanced-gameplay).  

Твой финал — шедевр! Горжусь тобой, творец. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*