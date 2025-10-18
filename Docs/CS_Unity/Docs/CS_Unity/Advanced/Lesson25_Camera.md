
# 📷 Урок 25: Камера — Следование за Героем

Привет, юный оператор арены! 💻 Добро пожаловать на двенадцатый уровень *Части 4* твоего квеста *UnityCSQuest*! Сегодня ты освоишь управление **камерой** в Unity, чтобы она следовала за игроком в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты добавишь плавное следование и эффект тряски для эпичности! Готов снять лучший кадр? Время на квест: 20–25 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController` из [Урок 24: Физика](../Advanced/Lesson24_Physics.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя съёмочная площадка!  

**Предупреждение**: Камера требует правильной настройки позиции и плавности. Сохраняй код (**Ctrl+S**) перед тестом, иначе камера не последует за игроком! Если камера дёргается, проверь `Lerp` или `Time.deltaTime`. Врубай Play Mode и снимай арену!

Готов запечатлеть героя? Погнали по уровням квеста! 🚀

## 🎯 **Что такое управление камерой?**
Камера в Unity определяет, что видит игрок.  
- **Transform**: Управляет позицией и вращением камеры.  
- **Lerp**: Плавная интерполяция для следования.  
- **Shake Effect**: Тряска камеры для динамики.  

В твоём шутере ты сделаешь камеру следующей за игроком с эффектом тряски.

*Ссылка на документацию*: [Camera](https://docs.unity3d.com/ScriptReference/Camera.html).

## 🔄 **Зачем это Нужно?**
Камера, следующая за игроком, улучшает обзор и погружение. В этом квесте ты:  
- Настроишь плавное следование камеры.  
- Добавишь тряску при смерти босса.  
- Сделаешь игру кинематографичной!  

**Почему это круто?**  
- **Обзор**: Игрок всегда в центре кадра.  
- **Эпичность**: Тряска усиливает эмоции.  
- **Для шутера**: Камера делает бой зрелищным!  

**Типичные ошибки новичков**:  
- Неправильная скорость интерполяции в `Lerp`.  
- Камера следует за неправильным объектом.  
- Отсутствие `Time.deltaTime` в движении.  

## ⚙️ **Квест: Настрой камеру**

### Уровень 1: Плавное следование  
1. **Создай скрипт `CameraFollow`:**  
   - В **Project** создай **C# Script**, назови `CameraFollow`.  
   - Реализуй следование за игроком:  
     ```csharp
     using UnityEngine;

     public class CameraFollow : MonoBehaviour
     {
         [SerializeField] private Transform target;
         [SerializeField] private Vector3 offset = new Vector3(0, 10, -10);
         [SerializeField] private float smoothSpeed = 0.125f;

         void LateUpdate()
         {
             if (target != null)
             {
                 Vector3 desiredPosition = target.position + offset;
                 Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
                 transform.position = smoothedPosition;
                 transform.LookAt(target);
             }
         }
     }
     ```

2. **Настрой `Main Camera`:**  
   - В **Hierarchy** выбери `Main Camera`, добавь компонент `CameraFollow`.  
   - Перетащи `Player` в поле `Target`.  
   - Установи `Offset` на `(0, 10, -10)` для топ-даун вида.  

3. **Настрой и протестируй:**  
   - Нажми **Play** — камера плавно следует за игроком!  

### Уровень 2: Эффект тряски  
1. **Обнови `CameraFollow`:**  
   - Добавь тряску:  
     ```csharp
     public void Shake(float duration, float magnitude)
     {
         StartCoroutine(ShakeCoroutine(duration, magnitude));
     }

     private IEnumerator ShakeCoroutine(float duration, float magnitude)
     {
         Vector3 originalPos = transform.position;
         float elapsed = 0f;

         while (elapsed < duration)
         {
             float x = Random.Range(-1f, 1f) * magnitude;
             float z = Random.Range(-1f, 1f) * magnitude;
             transform.position += new Vector3(x, 0, z);
             elapsed += Time.deltaTime;
             yield return null;
         }
         transform.position = Vector3.Lerp(transform.position, originalPos, Time.deltaTime);
     }
     ```

2. **Обнови `BossEnemy`:**  
   - Вызови тряску при смерти:  
     ```csharp
     protected override void Die()
     {
         GameManager.Instance.RemoveEnemy(this);
         EnemyDied?.Invoke(this);
         animator.SetTrigger("Die");
         Instantiate(explosionPrefab, transform.position, Quaternion.identity);
         AudioSource.PlayClipAtPoint(explosionSound, transform.position);
         Camera.main.GetComponent<CameraFollow>().Shake(0.5f, 0.5f);
         Debug.Log($"{gameObject.name} уничтожен с взрывом!");
         Destroy(gameObject, 0.5f);
     }
     ```

3. **Полный код `CameraFollow`:**  
   ```csharp
   using System.Collections;
   using UnityEngine;

   public class CameraFollow : MonoBehaviour
   {
       [SerializeField] private Transform target;
       [SerializeField] private Vector3 offset = new Vector3(0, 10, -10);
       [SerializeField] private float smoothSpeed = 0.125f;

       void LateUpdate()
       {
           if (target != null)
           {
               Vector3 desiredPosition = target.position + offset;
               Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
               transform.position = smoothedPosition;
               transform.LookAt(target);
           }
       }

       public void Shake(float duration, float magnitude)
       {
           StartCoroutine(ShakeCoroutine(duration, magnitude));
       }

       private IEnumerator ShakeCoroutine(float duration, float magnitude)
       {
           Vector3 originalPos = transform.position;
           float elapsed = 0f;

           while (elapsed < duration)
           {
               float x = Random.Range(-1f, 1f) * magnitude;
               float z = Random.Range(-1f, 1f) * magnitude;
               transform.position += new Vector3(x, 0, z);
               elapsed += Time.deltaTime;
               yield return null;
           }
           transform.position = Vector3.Lerp(transform.position, originalPos, Time.deltaTime);
       }
   }
   ```

4. **Настрой и протестируй:**  
   - Убедись, что `Main Camera` имеет `CameraFollow`.  
   - Нажми **Play**, уничтожь босса — камера трясётся!  

### Уровень 3: Ограничение камеры  
1. **Обнови `CameraFollow`:**  
   - Добавь ограничение позиции:  
     ```csharp
     [SerializeField] private Vector2 minBounds = new Vector2(-20, -20);
     [SerializeField] private Vector2 maxBounds = new Vector2(20, 20);

     void LateUpdate()
     {
         if (target != null)
         {
             Vector3 desiredPosition = target.position + offset;
             Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
             smoothedPosition.x = Mathf.Clamp(smoothedPosition.x, minBounds.x, maxBounds.x);
             smoothedPosition.z = Mathf.Clamp(smoothedPosition.z, minBounds.y, maxBounds.y);
             transform.position = smoothedPosition;
             transform.LookAt(target);
         }
     }
     ```

2. **Настрой и протестируй:**  
   - В **Inspector** установи `Min Bounds` на `(-20, -20)` и `Max Bounds` на `(20, 20)`.  
   - Нажми **Play** — камера не выходит за границы арены!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля `Target`, `Offset`, `Smooth Speed`, `Bounds` в `CameraFollow`.  
- В **Scene/Game**: Камера следует за игроком, трясётся при смерти босса, остаётся в границах.  
- В **Console**: Сообщения о тряске и действиях.  

## 💡 **Квесты: Прокачай камеру!**
1. **Базовый квест**:  
   - Увеличь `smoothSpeed` до 0.25f.  
   - Проверь: камера следует быстрее!  

2. **Квест на тряску**:  
   - Увеличь `duration` тряски до 1.0f:  
     ```csharp
     Camera.main.GetComponent<CameraFollow>().Shake(1.0f, 0.5f);
     ```  
   - Проверь: тряска длится дольше!  

3. **Квест на границы**:  
   - Измени `maxBounds` на `(30, 30)`.  
   - Проверь: камера охватывает большую арену!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь зум камеры при низком здоровье:  
     ```csharp
     void Start()
     {
         PlayerController.LowHealth += OnLowHealth;
     }

     void OnDestroy()
     {
         PlayerController.LowHealth -= OnLowHealth;
     }

     private void OnLowHealth()
     {
         GetComponent<Camera>().fieldOfView = Mathf.Lerp(GetComponent<Camera>().fieldOfView, 50, Time.deltaTime);
     }
     ```  
   - В `PlayerController` добавь событие `LowHealth` (см. Урок 18).  
   - Проверь: камера приближается при здоровье ≤ 30!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если камера не следует:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь `Target` в **Inspector**.  
  - Правильная ли скорость в `Lerp`?  
- **Хочешь эпичности?** В `Shake` добавь:  
  ```csharp
  Debug.Log("Камера трясётся!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Камера дёргается?** Проверь `smoothSpeed` и `Time.deltaTime`.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Камера, следующая за игроком, и эффекты, вроде тряски, делают игру зрелищной и погружающей. Это ключ к кинематографичности!

## Заключение: Камера — Твой Режиссёр Боя! 💻
Ты освоил управление камерой, добавив следование и тряску. Твой шутер стал кинематографичным! Это финал *Части 4*! Поздравляю, ты завершил продвинутый уровень! Следующий шаг — создай свою игру с нуля, используя все навыки.

**Что Далее?**  
- Начни новый проект или улучшай *UnityTopDownShooterQuest*!  
- Вопросы: [Unity Learn: Camera](https://learn.unity.com/tutorial/camera).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
