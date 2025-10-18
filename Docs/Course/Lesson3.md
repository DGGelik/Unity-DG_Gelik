# ⌨️ Урок 3: Движение Героя — Топ-Даун Навигация и Скрипты

Привет, мобильный манёвровщик! 🏃‍♂️ В этом уроке мы оживим прототип героя из Урока 1: добавим скрипт на C# для топ-даун движения (WASD для ходьбы, поворот к курсору мыши). Герой будет бегать по арене, как в классическом шутере, без сложной физики пока. Это введение в программирование — не бойся, код простой! Всё в одной сцене. Время: 20–30 минут.  

**Что тебе понадобится?**  
- Открытый проект "MyTopDownShooter" с ареной из Урока 2.  
- Базовые знания клавиатуры (WASD).  

**Предупреждение**: Если скрипт не компилируется — проверь ошибки в Console (Window > General > Console). Сохраняй перед Play!  

Готов заставить героя танцевать? По шагам, как по кодовым строкам! 📝

## Шаг 1: Создай Скрипт Движения — Твой Первый C# Код 🛠️
Скрипты — это "мозг" объектов. Мы напишем базовый для WASD.  

1. В **Project** (внизу) правой кнопкой на папке Assets → **Create > C# Script**. Назови "PlayerMovement".  
2. Двойной клик на скрипт — откроется Visual Studio (или встроенный редактор).  
3. Замени весь код на этот (скопируй-вставь):  
   ```
   using UnityEngine;

   public class PlayerMovement : MonoBehaviour
   {
       public float speed = 5f;  // Скорость движения

       void Update()
       {
           float horizontal = Input.GetAxis("Horizontal");  // A/D
           float vertical = Input.GetAxis("Vertical");      // W/S

           Vector3 movement = new Vector3(horizontal, 0f, vertical) * speed * Time.deltaTime;
           transform.Translate(movement);
       }
   }
   ```  
4. Сохрани скрипт (Ctrl+S). Вернись в Unity — код скомпилируется автоматически.  

**Готово!** Скрипт готов — он берёт ввод с клавиш и двигает объект.  
*Ссылка на помощь*: [Введение в Скрипты](https://docs.unity3d.com/Manual/ScriptingSection.html).  

## Шаг 2: Прикрепи Скрипт к Герою и Настрой Input — Активация Движения 🎮
Теперь герой послушается клавиш.  

1. В Hierarchy выбери Capsule (твой герой).  
2. В Inspector кликни **"Add Component"** → Ищи "PlayerMovement" → Выбери его.  
3. В компоненте PlayerMovement: Speed = 5 (или поэкспериментируй 3–10).  
4. Для поворота к мыши (топ-даун стиль): Добавь в скрипт (открой снова):  
   ```
   // Добавь в конец Update(), перед }
   Vector3 mousePosition = Camera.main.ScreenToWorldPoint(Input.mousePosition);
   mousePosition.y = transform.position.y;  // Остаёмся на плоскости
   Vector3 direction = mousePosition - transform.position;
   float angle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg;
   transform.rotation = Quaternion.Euler(0f, angle, 0f);
   ```  
5. Сохрани и вернись в Unity.  

**Готово!** Герой теперь двигается WASD и поворачивается к мыши.  
*Ссылка на помощь*: [Input Manager](https://docs.unity3d.com/ScriptReference/Input.GetAxis.html).  

## Шаг 3: Улучши Движение — Ограничения и Физика 🛡️
Чтобы герой не улетал за арену.  

1. Открой скрипт снова. Добавь в класс (после public float speed):  
   ```
   public Transform groundCheck;  // Для проверки земли (опционально)
   private Rigidbody rb;  // Для физики
   ```  
2. В Update() замени Translate на:  
   ```
   rb.velocity = new Vector3(horizontal * speed, rb.velocity.y, vertical * speed);
   ```  
3. В начале класса (после public class):  
   ```
   void Start()
   {
       rb = GetComponent<Rigidbody>();
   }
   ```  
4. Сохрани. В Inspector героя: Drag & Drop Rigidbody (если нет — добавь).  

**Готово!** Движение плавное, с физикой.  
*Ссылка на помощь*: [Rigidbody и Velocity](https://docs.unity3d.com/ScriptReference/Rigidbody.velocity.html).  

## Шаг 4: Сохрани и Протестируй — Движение в Действии ▶️
Проверим на арене.  

1. Сохрани сцену: **File > Save** (Ctrl+S).  
2. Кликни **Play**.  
3. В Game View: Нажми WASD — герой бегает! Двигай мышь — поворачивается.  
4. Кликни **Stop**. Если не двигается — проверь Console на ошибки (красный текст).  

**Ура!** Герой мобилен — основа навигации готова.  
*Ссылка на помощь*: [Отладка Скриптов](https://docs.unity3d.com/Manual/DebuggingScripts.html).  

## Что Далее? 🚀
- Перейди к [Уроку 4: HUD Шутера — UI для Очков, Здоровья и Боеприпасов](./Lesson4.md) — добавим интерфейс!  
- Вопросы: [Unity Learn: Player Movement](https://learn.unity.com/tutorial/moving-the-player).  

Твой герой в движении — битва начинается! Продолжай, тактик. 🔫  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*