
# 💥 Урок 34: Частицы через Код — Взрывы и Эффекты

Привет, юный пиротехник арены! 💻 Добро пожаловать на четвёртый уровень *Части 6* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **систему частиц** в Unity, чтобы добавить взрывы и эффекты в твой топ-даун шутер (*UnityTopDownShooterQuest*). Ты создашь частицы для попаданий пуль и смерти врагов через код! Готов зажечь арену? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow`, `EventPool`, `EnemyConfig` из [Урок 33: Префабы](../Additional/Lesson33_Prefabs.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `FastEnemy`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Particles в Assets — твоя пиротехническая мастерская!  

**Предупреждение**: Частицы требуют настройки **Particle System** и правильного управления. Сохраняй код (**Ctrl+S**) и префабы перед тестом, иначе эффекты не появятся! Если частицы не работают, проверь настройки **Particle System** и вызовы в коде. Врубай Play Mode и создавай взрывы!

Готов устроить фейерверк? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Система Частиц?**
**Particle System** в Unity создаёт визуальные эффекты (взрывы, искры, дым).  
- **ParticleSystem**: Компонент для управления частицами.  
- **Emit**: Запускает частицы через код.  
- **Modules**: Настройки (форма, цвет, размер).  

В твоём шутере ты создашь эффекты для попаданий пуль и смерти врагов.

*Ссылка на документацию*: [Particle System](https://docs.unity3d.com/Manual/PartSysReference.html).

## 🔄 **Зачем это Нужно?**
Частицы делают игру зрелищной и динамичной. В этом квесте ты:  
- Заменишь префаб `SparkEffect` на систему частиц.  
- Создашь взрывной эффект для смерти врагов.  
- Сделаешь игру эпичной!  

**Почему это круто?**  
- **Визуалы**: Эффекты усиливают погружение.  
- **Динамика**: Взрывы подчёркивают действия.  
- **Для шутера**: Частицы делают бои яркими!  

**Типичные ошибки новичков**:  
- Неправильные настройки модулей **Particle System**.  
- Забыл включить `Play` или `Emit` в коде.  
- Слишком много частиц, вызывающих лаги.  

## ⚙️ **Квест: Создай эффекты частиц**

### Уровень 1: Частицы для попаданий пуль  
1. **Создай систему частиц:**  
   - В **Project** → `Assets/Particles`, создай **Particle System**, назови `BulletHitEffect`.  
   - Настрой модули:  
     - **Main**: Duration 0.5, Start Size 0.1, Start Speed 2.  
     - **Emission**: Rate over Time 0, Bursts (Count 20, Time 0).  
     - **Shape**: Cone, Angle 30.  
     - **Renderer**: Material (яркий жёлтый).  

2. **Обнови `BulletController`:**  
   - Замени `SparkEffect` на частицы:  
     ```csharp
     [SerializeField] private ParticleSystem hitEffect;

     void OnCollisionEnter(Collision other)
     {
         if (other.gameObject.CompareTag("Enemy") || other.gameObject.CompareTag("Boss"))
         {
             other.gameObject.GetComponent<Enemy>().TakeDamage(10);
             ParticleSystem effect = Instantiate(hitEffect, other.contacts[0].point, Quaternion.identity);
             effect.Play();
             Destroy(effect.gameObject, 0.5f);
         }
         ReturnToPool();
     }
     ```

3. **Настрой `Bullet.prefab`:**  
   - Перетащи `BulletHitEffect` в поле `Hit Effect`.  

4. **Настрой и протестируй:**  
   - Нажми **Play**, стреляй во врагов — появляются искры при попадании!  

### Уровень 2: Взрыв при смерти врагов  
1. **Создай систему частиц:**  
   - В **Project** → `Assets/Particles`, создай **Particle System**, назови `EnemyExplosionEffect`.  
   - Настрой модули:  
     - **Main**: Duration 1.0, Start Size 0.2, Start Speed 5.  
     - **Emission**: Rate over Time 0, Bursts (Count 50, Time 0).  
     - **Shape**: Sphere, Radius 1.  
     - **Color over Lifetime**: Градиент от красного к прозрачному.  

2. **Обнови `Enemy`:**  
   - Замени `EnemyExplosion`:  
     ```csharp
     [SerializeField] protected ParticleSystem explosionEffect;

     protected virtual void Die()
     {
         GameManager.Instance.RemoveEnemy(this);
         EventPool.Instance.TriggerEvent("EnemyDeath", this);
         animator.SetTrigger("Die");
         ParticleSystem effect = Instantiate(explosionEffect, transform.position, Quaternion.identity);
         effect.Play();
         AudioSource.PlayClipAtPoint(explosionSound, transform.position);
         Camera.main.GetComponent<CameraFollow>().Shake(0.5f, 0.5f);
         Debug.Log($"{gameObject.name} уничтожен с взрывом!");
         Destroy(effect.gameObject, 1.0f);
         Destroy(gameObject, 0.5f);
     }
     ```

3. **Настрой `Enemy.prefab` и `Boss.prefab`:**  
   - Перетащи `EnemyExplosionEffect` в поле `Explosion Effect`.  

4. **Настрой и протестируй:**  
   - Нажми **Play**, уничтожай врагов — взрывы из частиц!  

### Уровень 3: Управление частицами через код  
1. **Обнови `BossEnemy`:**  
   - Добавь эффект мощного взрыва:  
     ```csharp
     protected override void Die()
     {
         GameManager.Instance.RemoveEnemy(this);
         EventPool.Instance.TriggerEvent("EnemyDeath", this);
         animator.SetTrigger("Die");
         ParticleSystem effect = Instantiate(explosionEffect, transform.position, Quaternion.identity);
         var main = effect.main;
         main.startSize = 0.3f; // Увеличенный размер
         effect.Emit(100); // Больше частиц
         AudioSource.PlayClipAtPoint(explosionSound, transform.position);
         Camera.main.GetComponent<CameraFollow>().Shake(0.5f, 0.5f);
         Debug.Log($"{gameObject.name} уничтожен с мощным взрывом!");
         Destroy(effect.gameObject, 1.0f);
         Destroy(gameObject, 0.5f);
     }
     ```

2. **Настрой и протестируй:**  
   - Нажми **Play**, уничтожь босса — мощный взрыв с большим количеством частиц!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для систем частиц в `BulletController` и `Enemy`.  
- В **Scene/Game**: Искры при попадании и взрывы при смерти врагов.  
- В **Console**: Логи взрывов и попаданий.  

## 💡 **Квесты: Прокачай частицы!**
1. **Базовый квест**:  
   - Увеличь `Start Size` в `BulletHitEffect` до 0.2.  
   - Проверь: искры крупнее!  

2. **Квест на взрыв**:  
   - Увеличь `Burst Count` в `EnemyExplosionEffect` до 80.  
   - Проверь: взрывы гуще!  

3. **Квест на управление**:  
   - В `BulletController` добавь изменение цвета:  
     ```csharp
     var main = effect.main;
     main.startColor = Color.cyan;
     ```  
   - Проверь: искры голубые!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь эффект дыма для бонусов:  
     - Создай `BonusSmokeEffect` (Particle System, Shape: Sphere, Color: серый).  
     - В `HealthBonus`:  
       ```csharp
       [SerializeField] private ParticleSystem smokeEffect;

       void OnTriggerEnter(Collider other)
       {
           if (other.CompareTag("Player"))
           {
               other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
               ParticleSystem effect = Instantiate(smokeEffect, transform.position, Quaternion.identity);
               effect.Play();
               Instantiate(healEffectPrefab, transform.position, Quaternion.identity);
               AudioSource.PlayClipAtPoint(bonusSound, transform.position);
               Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
               GameManager.Instance.RemoveBonus(this);
               EventPool.Instance.TriggerEvent("BonusCollected", healthBoost);
               Destroy(effect.gameObject, 1.0f);
               Destroy(gameObject);
           }
       }
       ```  
   - Проверь: бонусы испускают дым при сборе!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если частицы не появляются:  
  - Сохранил ли код и префабы (**Ctrl+S**)?  
  - Проверь настройки **Particle System**.  
  - Вызывается ли `Play` или `Emit`?  
- **Хочешь эпичности?** В `Die` добавь:  
  ```csharp
  Debug.Log("Эпичный взрыв частиц!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Лаги от частиц?** Уменьши `Burst Count` или `Duration`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Частицы делают игру зрелищной, подчёркивая ключевые моменты. Это ключ к эпичности!

## Заключение: Частицы — Твой Пиротехник Боя! 💻
Ты освоил частицы, добавив взрывы и искры. Твой шутер стал зрелищным! Следующий шаг — Scriptable Objects для гибких данных. Продолжай, пиротехник кода!

**Что Далее?**  
- Перейди к [Scriptable Objects — Гибкие Данные](../Additional/Lesson35_ScriptableObjects.md) — упрости настройки.  
- Вопросы: [Unity Learn: Particle System](https://learn.unity.com/tutorial/particle-system).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
