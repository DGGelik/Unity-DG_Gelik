
# 🌈 Урок 26: Шейдеры через Код — Визуальные Эффекты

Привет, юный художник арены! 💻 Добро пожаловать на первый уровень *Части 5* твоего квеста *UnityCSQuest*! Сегодня ты освоишь **шейдеры** в Unity, чтобы добавить визуальные эффекты в твой топ-даун шутер (*UnityTopDownShooterQuest*). Ты создашь шейдер для мигания игрока при получении урона и эффект свечения для бонусов! Готов раскрасить арену? Время на квест: 25–30 минут.

**Что тебе понадобится?**  
- Проект "UnityTopDownShooterQuest" (из [Создание Проекта](../../Preparation/CreateRun.md)).  
- Скрипты `PlayerController`, `Enemy`, `NormalEnemy`, `BossEnemy`, `EnemySpawner`, `BonusSpawner`, `BulletController`, `HealthBonus`, `GameManager`, `BulletPool`, `MenuController`, `CameraFollow` из [Урок 25: Камера](../Advanced/Lesson25_Camera.md).  
- Префабы `Bullet`, `Enemy`, `Boss`, `HealthBonus`, `EnemyExplosion`, `SparkEffect`, `HealEffect` в `Assets/Prefabs`.  
- Папка Materials в Assets — твоя мастерская шейдеров!  

**Предупреждение**: Шейдеры требуют настройки материалов и правильного кода. Сохраняй код (**Ctrl+S**) и материалы перед тестом, иначе эффекты не появятся! Если шейдеры не работают, проверь версию **Shader Graph** и настройки **Material**. Врубай Play Mode и создавай визуальную магию!

Готов зажечь арену? Погнали по уровням квеста! 🚀

## 🎯 **Что такое Шейдеры?**
**Шейдеры** в Unity — это программы, которые определяют, как рендерится объект (цвет, текстура, свечение).  
- **Shader Graph**: Визуальный редактор для создания шейдеров.  
- **Material**: Применяет шейдер к объекту.  
- **Properties**: Настраиваемые параметры шейдера.  

В твоём шутере ты создашь шейдеры для мигания игрока и свечения бонусов.

*Ссылка на документацию*: [Shader Graph](https://docs.unity3d.com/Manual/ShaderGraph.html).

## 🔄 **Зачем это Нужно?**
Шейдеры добавляют визуальную выразительность игре. В этом квесте ты:  
- Создашь эффект мигания игрока при уроне.  
- Добавишь свечение бонусам для привлекательности.  
- Сделаешь игру яркой и запоминающейся!  

**Почему это круто?**  
- **Визуалы**: Эффекты делают игру профессиональной.  
- **Погружение**: Мигание и свечение усиливают эмоции.  
- **Для шутера**: Эффекты подчёркивают ключевые моменты!  

**Типичные ошибки новичков**:  
- Неправильная настройка **Material** или **Shader Graph**.  
- Отсутствие **URP** (Universal Render Pipeline) в проекте.  
- Неправильные параметры в коде для шейдера.  

## ⚙️ **Квест: Создай визуальные эффекты**

### Подготовка: Настрой URP  
1. **Переведи проект на URP:**  
   - Убедись, что проект использует **Universal Render Pipeline** (установи через **Package Manager**).  
   - В **Project Settings** → **Graphics**, выбери **URP Asset**.  
   - Создай **URP Settings** в `Assets/Settings` и настрой.  

2. **Создай материалы:**  
   - В **Project** → `Assets/Materials`, создай материалы `PlayerFlash` и `BonusGlow`.  

### Уровень 1: Шейдер мигания игрока  
1. **Создай шейдер в Shader Graph:**  
   - В **Project** → `Assets/Shaders`, создай **Shader Graph** → **PBR Shader**, назови `PlayerFlashShader`.  
   - Открой шейдер, добавь **Property** `_FlashIntensity` (Float, Range 0–1).  
   - Добавь узлы:  
     - **Time** → **Sine** → умножь на `_FlashIntensity`.  
     - Подключи к **Emission** в **PBR Master**.  
   - Сохрани шейдер и назначь его на материал `PlayerFlash`.  

2. **Обнови `PlayerController`:**  
   - Добавь управление шейдером:  
     ```csharp
     [SerializeField] private Material flashMaterial;
     private Material originalMaterial;
     private Renderer playerRenderer;

     void Start()
     {
         // ... существующий код ...
         playerRenderer = GetComponent<Renderer>();
         originalMaterial = playerRenderer.material;
     }

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
             PlayerDied?.Invoke();
             Destroy(gameObject);
         }
         else
         {
             StartCoroutine(FlashEffect());
         }
     }

     private IEnumerator FlashEffect()
     {
         playerRenderer.material = flashMaterial;
         flashMaterial.SetFloat("_FlashIntensity", 1.0f);
         yield return new WaitForSeconds(0.5f);
         flashMaterial.SetFloat("_FlashIntensity", 0.0f);
         playerRenderer.material = originalMaterial;
     }
     ```

3. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `Player`, перетащи `PlayerFlash` в поле `Flash Material`.  
   - Нажми **Play**, получи урон — игрок мигает!  

### Уровень 2: Шейдер свечения бонусов  
1. **Создай шейдер в Shader Graph:**  
   - В **Project** → `Assets/Shaders`, создай **Shader Graph** → **PBR Shader**, назови `BonusGlowShader`.  
   - Добавь **Property** `_GlowIntensity` (Float, Range 0–2).  
   - Добавь узлы:  
     - **Fresnel Effect** → умножь на `_GlowIntensity`.  
     - Подключи к **Emission** в **PBR Master**.  
   - Сохрани шейдер и назначь его на материал `BonusGlow`.  

2. **Обнови `HealthBonus`:**  
   - Примени материал:  
     ```csharp
     void Start()
     {
         GameManager.Instance.AddBonus(this);
         healthBoost = Random.Range(20, 50);
         Rigidbody rb = GetComponent<Rigidbody>();
         rb.AddForce(Vector3.up * 3.0f, ForceMode.Impulse);
         GetComponent<Renderer>().material = Resources.Load<Material>("Materials/BonusGlow");
         Debug.Log($"Бонус с здоровьем: {healthBoost}, подпрыгнул!");
     }
     ```

3. **Настрой и протестируй:**  
   - Убедись, что `BonusGlow` находится в `Assets/Resources/Materials`.  
   - Нажми **Play** — бонусы светятся!  

### Уровень 3: Управление шейдером через код  
1. **Обнови `HealthBonus`:**  
   - Добавь пульсацию свечения:  
     ```csharp
     [SerializeField] private Material glowMaterial;

     void Update()
     {
         transform.Rotate(0, 90 * Time.deltaTime, 0);
         float pulse = Mathf.Sin(Time.time * 2.0f) * 0.5f + 0.5f;
         glowMaterial.SetFloat("_GlowIntensity", pulse);
     }
     ```

2. **Настрой и протестируй:**  
   - В **Hierarchy** выбери `HealthBonus.prefab`, перетащи `BonusGlow` в поле `Glow Material`.  
   - Нажми **Play** — бонусы пульсируют светом!  

## 💻 **Что ты увидишь?**
- В **Inspector**: Поля для материалов в `PlayerController` и `HealthBonus`.  
- В **Scene/Game**: Игрок мигает при уроне, бонусы светятся и пульсируют.  
- В **Console**: Сообщения о визуальных эффектах.  

## 💡 **Квесты: Прокачай шейдеры!**
1. **Базовый квест**:  
   - Увеличь длительность мигания игрока до 1.0f:  
     ```csharp
     yield return new WaitForSeconds(1.0f);
     ```  
   - Проверь: мигание длится дольше!  

2. **Квест на свечение**:  
   - Увеличь `_GlowIntensity` до Range 0–3 в `BonusGlowShader`.  
   - Проверь: бонусы светятся ярче!  

3. **Квест на пульсацию**:  
   - Ускорь пульсацию бонусов:  
     ```csharp
     float pulse = Mathf.Sin(Time.time * 4.0f) * 0.5f + 0.5f;
     ```  
   - Проверь: свечение пульсирует быстрее!  

4. **Квест со звёздочкой (для мастеров арены) 🌟**:  
   - Добавь мигание врагам при уроне:  
     - В `Enemy`:  
       ```csharp
       [SerializeField] private Material flashMaterial;
       private Material originalMaterial;
       private Renderer enemyRenderer;

       protected virtual void Start()
       {
           // ... существующий код ...
           enemyRenderer = GetComponent<Renderer>();
           originalMaterial = enemyRenderer.material;
       }

       public virtual void TakeDamage(int damage)
       {
           currentHealth -= damage;
           Debug.Log($"{gameObject.name} получил урон! Осталось здоровья: {currentHealth}");
           Rigidbody rb = GetComponent<Rigidbody>();
           Vector3 pushDirection = (transform.position - GameObject.FindGameObjectWithTag("Player").transform.position).normalized;
           rb.AddForce(pushDirection * 5.0f, ForceMode.Impulse);
           StartCoroutine(FlashEffect());
           if (currentHealth <= 0)
           {
               Die();
           }
       }

       private IEnumerator FlashEffect()
       {
           enemyRenderer.material = flashMaterial;
           flashMaterial.SetFloat("_FlashIntensity", 1.0f);
           yield return new WaitForSeconds(0.3f);
           flashMaterial.SetFloat("_FlashIntensity", 0.0f);
           enemyRenderer.material = originalMaterial;
       }
       ```  
   - Проверь: враги мигают при попадании!  

## 🔄 **Советы для героев кода**
- **Ошибки?** Если шейдеры не работают:  
  - Сохранил ли код и шейдеры (**Ctrl+S**)?  
  - Используется ли URP?  
  - Правильный ли материал в **Inspector**?  
- **Хочешь эпичности?** В `FlashEffect` добавь:  
  ```csharp
  Debug.Log("Эффект мигания активирован!");
  ```  
  - Увидишь сообщение в **Console**!  
- **Эффекты не видны?** Проверь настройки **Shader Graph** и материалы.  

## ⚙️ **Зачем это пригодится в твоём шутере?**
Шейдеры делают игру визуально яркой, подчёркивая ключевые моменты. Это ключ к погружению!

## Заключение: Шейдеры — Твоя Визуальная Магия! 💻
Ты освоил шейдеры, добавив мигание и свечение. Твой шутер стал ярким! Следующий шаг — AI для врагов. Продолжай, художник кода!

**Что Далее?**  
- Перейди к [AI Врагов — Простая Логика Поведения](../Master/Lesson27_AI.md) — оживи врагов.  
- Вопросы: [Unity Learn: Shader Graph](https://learn.unity.com/tutorial/shader-graph).  

[Назад к оглавлению](../CS_Unity.md)  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*
