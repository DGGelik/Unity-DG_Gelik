
# 📡 Урок 28: Пул Событий — Глобальные Сигналы

Привет, юный связист арены! 💻 Добро пожаловать на третий уровень *Части 5* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **пул событий** в C#, чтобы создать централизованную систему сигналов в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты заменишь статические события на глобальный пул для большей гибкости! Готов объединить сигналы? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow` из [Урок 27: AI](../Master/Lesson27_AI.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя станция глобальных сигналов!  

**Предупреждение**: Пул событий требует аккуратной подписки и отписки. Сохраняй код (**Ctrl+S**) перед тестом, иначе сигналы не дойдут! Если события не срабатывают, проверь ключи и методы в пуле. Врубай Play Mode и налаживай связь!

Готов отправить глобальные сигналы? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Пул Событий?**
**Пул событий** — это централизованная система для управления событиями, заменяющая статические делегаты.  
- **Dictionary**: Хранит события по ключам (строкам).  
- **Action**: Гибкие делегаты для событий.  
- **Singleton**: Обеспечивает глобальный доступ к пулу.  

В твоём шутере ты создашь пул для событий смерти игрока, врагов и сбора бонусов.

*Ссылка на документацию*: [Events](https://docs.microsoft.com/en-us/dotnet/csharp/events).

## 🔄 **Зачем это Нужно?**
Пул событий упрощает управление сигналами, делая код чище. В этом квесте ты:  
- Заменишь статические события в `PlayerController`, `Enemy`, `HealthBonus`.  
- Создашь глобальный пул событий в `GameManager`.  
- Сделаешь игру более модульной!  

**Почему это круто?**  
- **Гибкость**: Легко добавлять новые события.  
- **Чистота кода**: Меньше статических делегатов.  
- **Для шутера**: Централизованные сигналы упрощают реакции!  

**Типичные ошибки новичков**:  
- Неправильные ключи в пуле событий.  
- Забыл подписаться/отписаться от событий.  
- Вызов события без проверки подписчиков.  

## ⚙️ **Квест: Создай пул событий**

### Уровень 1: Создание пула событий  
1. **Создай скрипт `EventPool`:**  
   - В **Project** создай **C# Script**, назови `EventPool`.  
   - Реализуй пул событий:  
     ```csharp
     using System;
     using System.Collections.Generic;
     using UnityEngine;

     public class EventPool : MonoBehaviour
     {
         public static EventPool Instance { get; private set; }
         private Dictionary<string, Action<object>> eventDictionary = new Dictionary<string, Action<object>>();

         void Awake()
         {
             if (Instance == null)
             {
                 Instance = this;
                 DontDestroyOnLoad(gameObject);
             }
             else
             {
                 Destroy(gameObject);
             }
         }

         public void Subscribe(string eventName, Action<object> callback)
         {
             if (!eventDictionary.ContainsKey(eventName))
             {
                 eventDictionary[eventName] = null;
             }
             eventDictionary[eventName] += callback;
         }

         public void Unsubscribe(string eventName, Action<object> callback)
         {
             if (eventDictionary.ContainsKey(eventName))
             {
                 eventDictionary[eventName] -= callback;
             }
         }

         public void TriggerEvent(string eventName, object data = null)
         {
             if (eventDictionary.ContainsKey(eventName))
             {
                 eventDictionary[eventName]?.Invoke(data);
                 Debug.Log($"Событие {eventName} вызвано!");
             }
         }
     }
     ```

2. **Настрой `EventPool`:**  
   - В **Hierarchy** создай пустой объект `EventPool`, добавь компонент `EventPool`.  

### Уровень 2: Переработка событий игрока  
1. **Обнови `PlayerController`:**  
   - Убери статическое событие и используй пул:  
     ```csharp
     public void TakeDamage(int damage)
     {
         currentHealth -= damage;
         healthBar.value = currentHealth;
         Debug.Log(playerName + " получил урон! Осталось здоровья: " + currentHealth);
         if (damage < 0)
         {
             GameManager.Instance.AddScore(10);
         }
         if (currentHealth <= 0)
         {
             Debug.Log(playerName + " повержен!");
             EventPool.Instance.TriggerEvent("PlayerDeath");
             Destroy(gameObject);
         }
         else
         {
             StartCoroutine(FlashEffect());
         }
     }
     ```

2. **Обнови `GameManager`:**  
   - Подпишись на событие:  
     ```csharp
     void OnEnable()
     {
         EventPool.Instance.Subscribe("PlayerDeath", OnPlayerDeath);
         EventPool.Instance.Subscribe("EnemyDeath", OnEnemyDeath);
         EventPool.Instance.Subscribe("BonusCollected", OnBonusCollected);
     }

     void OnDisable()
     {
         EventPool.Instance.Unsubscribe("PlayerDeath", OnPlayerDeath);
         EventPool.Instance.Unsubscribe("EnemyDeath", OnEnemyDeath);
         EventPool.Instance.Unsubscribe("BonusCollected", OnBonusCollected);
     }
     ```

3. **Настрой и протестируй:**  
   - Нажми **Play**, доведи здоровье игрока до 0 — игра перезапускается через пул событий!  

### Уровень 3: Переработка событий врагов и бонусов  
1. **Обнови `Enemy`:**  
   - Замени событие:  
     ```csharp
     protected virtual void Die()
     {
         GameManager.Instance.RemoveEnemy(this);
         EventPool.Instance.TriggerEvent("EnemyDeath", this);
         animator.SetTrigger("Die");
         Instantiate(explosionPrefab, transform.position, Quaternion.identity);
         AudioSource.PlayClipAtPoint(explosionSound, transform.position);
         Camera.main.GetComponent<CameraFollow>().Shake(0.5f, 0.5f);
         Debug.Log($"{gameObject.name} уничтожен с взрывом!");
         Destroy(gameObject, 0.5f);
     }
     ```

2. **Обнови `HealthBonus`:**  
   - Замени событие:  
     ```csharp
     void OnTriggerEnter(Collider other)
     {
         if (other.CompareTag("Player"))
         {
             other.GetComponent<PlayerController>().TakeDamage(-healthBoost);
             Instantiate(healEffectPrefab, transform.position, Quaternion.identity);
             AudioSource.PlayClipAtPoint(bonusSound, transform.position);
             Debug.Log("Игрок собрал бонус здоровья! +" + healthBoost + " здоровья");
             GameManager.Instance.RemoveBonus(this);
             EventPool.Instance.TriggerEvent("BonusCollected", this);
             Destroy(gameObject);
         }
     }
     ```

3. **Настрой и протестируй:**  
   - Нажми **Play**, уничтожай врагов и собирай бонусы — очки начисляются через пул!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля в `GameManager` для подписки.  
- В **Scene/Game**: События срабатывают через пул (смерть, уничтожение, сбор).  
- В **Console**: Сообщения о вызовах событий.  

## 💡 **Квесты: Прокачай пул событий!**
1. **Базовый квест**:  
   - В `EventPool` добавь логирование:  
     ```csharp
     Debug.Log($"Подписка на событие {eventName}");
     ```  
   - Проверь: сообщения о подписке в **Console**!  

2. **Квест на врагов**:  
   - В `GameManager` добавь больше очков за боссов:  
     ```csharp
     private void OnEnemyDeath(object data)
     {
         Enemy enemy = (Enemy)data;
         AddScore(enemy is BossEnemy ? 150 : 50);
         Debug.Log($"Событие: Враг {enemy.name} уничтожен, добавлено очков!");
         PlayerPrefs.SetInt("WaveNumber", enemySpawner.GetWaveNumber());
         PlayerPrefs.Save();
     }
     ```  
   - Проверь: боссы дают 150 очков!  

3. **Квест на бонусы**:  
   - В `HealthBonus` добавь данные в событие:  
     ```csharp
     EventPool.Instance.TriggerEvent("BonusCollected", healthBoost);
     ```  
   - В `GameManager`:  
     ```csharp
     private void OnBonusCollected(object data)
     {
         int boost = (int)data;
         AddScore(20 + boost / 10);
         Debug.Log($"Событие: Бонус собран, добавлено {20 + boost / 10} очков!");
     }
     ```  
   - Проверь: бонусы дают больше очков!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь событие для низкого здоровья:  
     - В `PlayerController`:  
       ```csharp
       if (currentHealth <= 30 && currentHealth > 0)
       {
           EventPool.Instance.TriggerEvent("LowHealth");
       }
       ```  
     - В `CameraFollow`:  
       ```csharp
       void OnEnable()
       {
           EventPool.Instance.Subscribe("LowHealth", OnLowHealth);
       }

       void OnDisable()
       {
           EventPool.Instance.Unsubscribe("LowHealth", OnLowHealth);
       }

       private void OnLowHealth(object data)
       {
           GetComponent<Camera>().fieldOfView = Mathf.Lerp(GetComponent<Camera>().fieldOfView, 50, Time.deltaTime);
       }
       ```  
   - Проверь: камера зумирует при низком здоровье!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если события не срабатывают:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь ключи в `EventPool`.  
  - Подписка/отписка в `OnEnable`/`OnDisable`?  
- **Хочешь эпичности?** В `TriggerEvent` добавь:  
  ```csharp
  Debug.Log($"Событие {eventName} с данными: {data}");
  ```  
  - Увидишь данные в **Console**!  
- **События не доходят?** Проверь `eventDictionary`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Пул событий делает код чище и гибче, упрощая добавление новых реакций. Это ключ к модульности!

## Заключение: Пул Событий — Твой Глобальный Связист! 💻
Ты освоил пул событий, объединив сигналы. Твой шутер стал модульным! Следующий шаг — оптимизация игры. Продолжай, связист кода!

**Что Далее?**  
- Перейди к [Оптимизация — Делай Игру Быстрее](../Master/Lesson29_Optimization.md) — ускорь игру.  
- Вопросы: [Unity Learn: Events](https://learn.unity.com/tutorial/events).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
