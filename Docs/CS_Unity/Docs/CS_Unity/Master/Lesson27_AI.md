
# 🤖 Урок 27: AI Врагов — Простая Логика Поведения

Привет, юный стратег арены! 💻 Добро пожаловать на второй уровень *Части 5* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **AI** (искусственный интеллект) для врагов в твоём топ-даун шутере (*UnityTopDownShooterQuest*). Ты добавишь патрулирование и преследование игрока для обычных врагов и сложное поведение для боссов! Готов оживить врагов? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow` из [Урок 26: Шейдеры](../Master/Lesson26_Shaders.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Scripts в Assets — твоя лаборатория AI!  

**Предупреждение**: AI требует точной настройки состояний и условий. Сохраняй код (**Ctrl+S**) перед тестом, иначе враги не оживут! Если враги ведут себя странно, проверь условия переходов и расстояния. Врубай Play Mode и оживи арену!

Готов создать умных врагов? Погнали по уровням квеста! 🚀

## 🎯 **Что такое AI врагов?**
**AI** в играх — это логика поведения NPC (врагов, союзников).  
- **Finite State Machine (FSM)**: Состояния (патруль, атака) и переходы между ними.  
- **Vector3.Distance**: Проверяет расстояние до цели.  
- **NavMesh** (опционально): Упрощает навигацию (но мы используем простой подход).  

В твоём шутере ты добавишь патрулирование и преследование для врагов.

*Ссылка на документацию*: [Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html).

## 🔄 **Зачем это Нужно?**
AI делает врагов умными и разнообразными. В этом квесте ты:  
- Научишь обычных врагов патрулировать и преследовать.  
- Добавишь боссам сложное поведение (атака с паузами).  
- Сделаешь бои динамичными и интересными!  

**Почему это круто?**  
- **Разнообразие**: Враги ведут себя по-разному.  
- **Вызов**: Умные враги требуют стратегии.  
- **Для шутера**: AI делает бои напряжёнными!  

**Типичные ошибки новичков**:  
- Неправильные условия переходов между состояниями.  
- Забыл обновлять цель AI в `Update`.  
- Слишком большие/маленькие расстояния для логики.  

## ⚙️ **Квест: Оживи врагов**

### Уровень 1: Патрулирование обычных врагов  
1. **Обнови `NormalEnemy`:**  
   - Добавь состояния и патрулирование:  
     ```csharp
     private enum State { Patrol, Chase }
     private State currentState = State.Patrol;
     private Vector3 patrolPoint;
     [SerializeField] private float patrolRange = 5.0f;
     [SerializeField] private float chaseDistance = 8.0f;

     protected override void Start()
     {
         base.Start();
         player = GameObject.FindGameObjectWithTag("Player").transform;
         patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
     }

     void Update()
     {
         if (player == null) return;

         float distanceToPlayer = Vector3.Distance(transform.position, player.position);
         currentState = distanceToPlayer < chaseDistance ? State.Chase : State.Patrol;

         if (currentState == State.Patrol)
         {
             Vector3 direction = (patrolPoint - transform.position).normalized;
             transform.Translate(direction * moveSpeed * Time.deltaTime);
             if (Vector3.Distance(transform.position, patrolPoint) < 0.5f)
             {
                 patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
             }
         }
         else
         {
             Vector3 direction = (player.position - transform.position).normalized;
             float wave = Mathf.Sin(Time.time * 2.0f) * 0.5f;
             Vector3 waveOffset = new Vector3(wave, 0, 0);
             transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
         }
     }
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Enemy.prefab`, установи `Patrol Range` на 5.0, `Chase Distance` на 8.0.  
   - Нажми **Play** — враги патрулируют и преследуют игрока, если он близко!  

### Уровень 2: AI босса с атакой  
1. **Обнови `BossEnemy`:**  
   - Добавь атаку с паузами:  
     ```csharp
     private enum State { Idle, Chase, Attack }
     private State currentState = State.Idle;
     [SerializeField] private float chaseDistance = 10.0f;
     [SerializeField] private float attackDistance = 3.0f;
     [SerializeField] private float attackCooldown = 2.0f;
     private float attackTimer = 0f;

     void Update()
     {
         if (player == null) return;

         float distanceToPlayer = Vector3.Distance(transform.position, player.position);
         if (distanceToPlayer < attackDistance)
             currentState = State.Attack;
         else if (distanceToPlayer < chaseDistance)
             currentState = State.Chase;
         else
             currentState = State.Idle;

         attackTimer -= Time.deltaTime;

         switch (currentState)
         {
             case State.Idle:
                 break;
             case State.Chase:
                 Vector3 direction = (player.position - transform.position).normalized;
                 speedMultiplier = Mathf.Lerp(speedMultiplier, 1.5f, Time.deltaTime * 0.5f);
                 transform.Translate(direction * moveSpeed * speedMultiplier * Time.deltaTime);
                 break;
             case State.Attack:
                 if (attackTimer <= 0)
                 {
                     Attack();
                     attackTimer = attackCooldown;
                 }
                 break;
         }
     }

     private void Attack()
     {
         if (player != null)
         {
             player.GetComponent<PlayerController>().TakeDamage(damage);
             Debug.Log($"{gameObject.name} атаковал игрока!");
         }
     }
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Boss.prefab`, установи `Chase Distance` на 10.0, `Attack Distance` на 3.0, `Attack Cooldown` на 2.0.  
   - Нажми **Play** — босс атакует, когда игрок близко!  

### Уровень 3: Реакция на бонусы  
1. **Обнови `NormalEnemy`:**  
   - Добавь движение к бонусам:  
     ```csharp
     [SerializeField] private float bonusChaseDistance = 6.0f;

     void Update()
     {
         if (player == null) return;

         HealthBonus nearestBonus = FindNearestBonus();
         float distanceToPlayer = Vector3.Distance(transform.position, player.position);
         float distanceToBonus = nearestBonus != null ? Vector3.Distance(transform.position, nearestBonus.transform.position) : float.MaxValue;

         if (distanceToBonus < bonusChaseDistance)
         {
             Vector3 direction = (nearestBonus.transform.position - transform.position).normalized;
             transform.Translate(direction * moveSpeed * Time.deltaTime);
         }
         else if (distanceToPlayer < chaseDistance)
         {
             currentState = State.Chase;
             Vector3 direction = (player.position - transform.position).normalized;
             float wave = Mathf.Sin(Time.time * 2.0f) * 0.5f;
             Vector3 waveOffset = new Vector3(wave, 0, 0);
             transform.Translate((direction + waveOffset) * moveSpeed * Time.deltaTime);
         }
         else
         {
             currentState = State.Patrol;
             Vector3 direction = (patrolPoint - transform.position).normalized;
             transform.Translate(direction * moveSpeed * Time.deltaTime);
             if (Vector3.Distance(transform.position, patrolPoint) < 0.5f)
             {
                 patrolPoint = transform.position + new Vector3(Random.Range(-patrolRange, patrolRange), 0, Random.Range(-patrolRange, patrolRange));
             }
         }
     }

     private HealthBonus FindNearestBonus()
     {
         var bonuses = GameManager.Instance.GetBonuses();
         HealthBonus nearest = null;
         float minDistance = float.MaxValue;
         foreach (var bonus in bonuses)
         {
             float distance = Vector3.Distance(transform.position, bonus.transform.position);
             if (distance < minDistance)
             {
                 minDistance = distance;
                 nearest = bonus;
             }
         }
         return nearest;
     }
     ```

2. **Обнови `GameManager`:**  
   - Добавь метод для бонусов:  
     ```csharp
     public List<HealthBonus> GetBonuses()
     {
         return bonuses;
     }
     ```

3. **Настрой и протестируй:**  
   - Нажми **Play** — враги движутся к ближайшим бонусам, если они ближе игрока!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для расстояний и кулдаунов в `NormalEnemy` и `BossEnemy`.  
- В **Scene/Game**: Враги патрулируют, преследуют игрока или бонусы, боссы атакуют.  
- В **Console**: Сообщения об атаках и состояниях.  

## 💡 **Квесты: Прокачай AI!**
1. **Базовый квест**:  
   - Увеличь `chaseDistance` в `NormalEnemy` до 10.0f.  
   - Проверь: враги преследуют с большего расстояния!  

2. **Квест на босса**:  
   - Уменьши `attackCooldown` в `BossEnemy` до 1.0f.  
   - Проверь: босс атакует чаще!  

3. **Квест на бонусы**:  
   - Увеличь `bonusChaseDistance` до 8.0f.  
   - Проверь: враги охотятся за бонусами дальше!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь уклонение врагов от пуль:  
     ```csharp
     void OnTriggerEnter(Collider other)
     {
         if (other.CompareTag("Bullet"))
         {
             Vector3 dodgeDirection = Vector3.Cross(Vector3.up, (other.transform.position - transform.position).normalized);
             transform.Translate(dodgeDirection * moveSpeed * Time.deltaTime);
         }
     }
     ```  
   - Проверь: враги уклоняются от пуль!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если AI не работает:  
  - Сохранил ли код (**Ctrl+S**)?  
  - Проверь условия переходов (`distanceToPlayer`).  
  - Есть ли цель (`player != null`)?  
- **Хочешь эпичности?** В `Attack` добавь:  
  ```csharp
  Debug.Log("Босс нанёс мощный удар!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Враги застревают?** Проверь `moveSpeed` и коллизии.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
AI делает врагов умными, добавляя разнообразие и вызов. Это ключ к напряжённым боям!

## Заключение: AI — Твой Стратег Боя! 💻
Ты освоил AI, оживив врагов. Твой шутер стал умнее! Следующий шаг — пул событий для глобальных сигналов. Продолжай, стратег кода!

**Что Далее?**  
- Перейди к [Пул Событий — Глобальные Сигналы](../Master/Lesson28_EventPool.md) — объедини события.  
- Вопросы: [Unity Learn: AI](https://learn.unity.com/tutorial/ai).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
