# ❓ FAQ и Подсказки: Мудрость Странников

Привет, искатель ответов! ❓ Эта глава дополнительной теории — твой полный гид по **FAQ (часто задаваемым вопросам) и подсказкам** для Unity и твоего топ-даун шутера. Здесь собраны типичные проблемы новичков (от "почему объект не двигается?" до "как фиксить лаги спавна?"), быстрые фиксы и советы по курсу. Не стесняйся — баги случаются у всех, а сообщество (см. главу Сообщество) поможет с остальным. Это "спасательный круг" — читай перед паникой! Время на чтение: 15–25 минут.  

**Что тебе понадобится?**  
- Открытый проект на Unity (любой из курса).  
- Console (Window > General > Console) для проверки логов.  

**Предупреждение**: Если баг критичный (краш) — сохрани проект (File > Save Project), сделай бэкап. Для специфических ошибок — скопируй из Console и гугли "Unity [error message]". Если ничего не помогает — issue на GitHub репо. Подсказки — не замена docs, а старт.  

Готов к фиксам? По категориям, как по чек-листу! 📝

## 🎯 **FAQ: Часто Задаваемые Вопросы по Курсу и Unity**
Вот топ-10 вопросов от учеников — с ответами. Если твой не здесь — ищи похожий или спрашивай в сообществе.

### **1. Почему Unity крашится при запуске/Play?**
**Ответ**: Обычно из-за плагинов (Visual Studio Tools) или памяти. Фикс:  
- Закрой другие программы.  
- Project Settings > Player > Other Settings > Configuration = Release (временно).  
- Если ассет — удали и переимпортируй.  
*Совет для Шутера*: Если при спавне волны — уменьши waveSize до 1.  
*Ссылка*: [Unity Crashes](https://docs.unity3d.com/Manual/TroubleShooting.html).

### **2. Объект не двигается (скрипт на Transform работает в Update, но не в Play)?**
**Ответ**: Забыл Time.deltaTime или Space.World. Фикс:  
```csharp
transform.Translate(movement * Time.deltaTime, Space.World);  // Плавно, глобально
```
Или Rigidbody: Используй rb.velocity вместо Translate.  
*Совет для Шутера*: Для героя — FixedUpdate() для физики.  
*Ссылка*: [Movement Basics](https://docs.unity3d.com/Manual/MonoBehaviourFixedUpdate.html).

### **3. Пули проходят сквозь врагов (нет столкновений)?**
**Ответ**: Нет Collider или Tag. Фикс:  
- Добавь Sphere Collider к пуле (Is Trigger = On).  
- Tag "Enemy" на враге.  
- В OnTriggerEnter: if (other.CompareTag("Enemy")).  
*Совет для Шутера*: Collision Detection = Continuous в Rigidbody пули.  
*Ссылка*: [Colliders](https://docs.unity3d.com/Manual/CollidersOverview.html).

### **4. UI не обновляется (ScoreText.text меняется, но на экране старое)?**
**Ответ**: Ссылка null или Canvas не активен. Фикс:  
- Перетащи Text в public поле скрипта.  
- Canvas > Render Mode = Screen Space - Overlay.  
*Совет для Шутера*: UpdateUI() в LateUpdate() для синхрона.  
*Ссылка*: [UI Scripting](https://docs.unity3d.com/Manual/UIScripting.html).

### **5. Анимация не играет (Animator пустой или триггер не срабатывает)?**
**Ответ**: Нет Animator Controller или параметр не Set. Фикс:  
- Перетащи контроллер в Animator компонент.  
- animator.SetTrigger("Shoot"); в скрипте.  
*Совет для Шутера*: Has Exit Time = Off в переходах.  
*Ссылка*: [Animator Troubleshooting](https://docs.unity3d.com/Manual/AnimationStateMachine.html).

### **6. FPS низкий (лаги в бою, <30 кадров)?**
**Ответ**: Много Instantiate или частиц. Фикс:  
- Object Pooling: Переиспользуй пули (Queue<GameObject>).  
- Profiler (Window > Analysis > Profiler) — найди bottleneck (CPU от спавна).  
*Совет для Шутера*: Fixed Timestep = 0.02 в Time Settings.  
*Ссылка*: [Performance Optimization](https://docs.unity3d.com/Manual/OptimizingPerformance.html).

### **7. Сборка .exe не работает (черный экран или краш)?**
**Ответ**: Сцены не в Build или missing references. Фикс:  
- Build Settings > Add Open Scenes.  
- Console на ошибки (Development Build = On).  
*Совет для Шутера*: Standalone > Target Platform = PC.  
*Ссылка*: [Building Troubleshooting](https://docs.unity3d.com/Manual/BuildSettings.html).

### **8. Скрипт не компилируется (красные ошибки в Console)?**
**Ответ**: Синтаксис ( ; или {} ) или using. Фикс:  
- Добавь using UnityEngine; using System.Collections;.  
- VS: Build > Rebuild Solution.  
*Совет для Шутера*: Если Null — проверь [SerializeField].  
*Ссылка*: [Script Compilation](https://docs.unity3d.com/Manual/ScriptCompilation.html).

### **9. Ассеты из Store не импортируются (розовые модели)?**
**Ответ**: Несовместимость версии. Фикс:  
- Import Settings > Model > Scale Factor = 1.  
- Переимпортируй (правой > Reimport).  
*Совет для Шутера*: Free Low Poly Pack — для врагов.  
*Ссылка*: [Asset Import](https://docs.unity3d.com/Manual/HOWTO-importObject.html).

### **10. Как балансировать игру (враги слишком лёгкие/сложные)?**
**Ответ**: Итеративно: Playtest 10 мин, твичь SO (damage +10%). Фикс:  
- ScriptableObjects для stats (из Продвинутые Техники).  
- Profiler для времени волны.  
*Совет для Шутера*: WaveSize = 3–10, test на друзьях.  
*Ссылка*: [Game Balancing](https://learn.unity.com/tutorial/game-balancing).

## 🔧 **Подсказки: Быстрые Фиксы по Темам**
Короткие хаки — копируй в код.

### **Движение и Физика**
- Герой скользит: rb.drag = 5f в Rigidbody.  
- Пули тормозят: Use Gravity = Off.  
- Откат от выстрела: rb.AddForce(-transform.forward * recoil, ForceMode.Impulse);.

### **UI и Анимация**
- Текст не виден: Canvas > Render Mode = Overlay.  
- Анимация заикается: animator.updateMode = AnimatorUpdateMode.Normal.  
- Кнопка не кликабельна: Raycast Target = On в Image.

### **Скрипты и Баги**
- Null prefab: if (prefab == null) Debug.LogError("Prefab missing!");.  
- Memory leak: Destroy(gameObject) в OnDestroy.  
- Random не рандомный: Random.InitState((int)System.DateTime.Now.Ticks);.

### **Сборка и Оптимизация**
- Билд большой: Compression = LZ4HC в Build Settings.  
- FPS дроп: Quality Settings > Shadows = Disable.  
- Мобильный: Target API Level = Auto.

### **Курс-Специфично**
- Урок 3 (движение): Space.World в Translate.  
- Урок 7 (пули): Continuous Collision в Rigidbody.  
- Урок 10 (портфолио): OBS для записи, itch.io для аплоада.

**Быстрый Чеклист Багов**:
| Симптом | Фикс |
|---------|------|
| Объект невидим | Renderer enabled = On. |
| Звук не играет | AudioListener на камере. |
| Сцена не грузится | В Build Settings. |

## 💡 **Продвинутые Подсказки: Когда Обратиться в Сообщество**
- Локальный баг: Console + гугл "Unity [error] 2023".  
- Дизайн: r/gamedev "Balance top-down shooter waves".  
- Ассеты: Asset Store "free [need]".  
- Карьера: LinkedIn "Unity junior developer".  

**Эксперимент**: Открой Console, добавь Debug.Log("Test") — увидишь в Play.

*Ссылка на продвинутые ресурсы*: [Unity Troubleshooting](https://docs.unity3d.com/Manual/TroubleShooting.html).

## Заключение: FAQ — Твой Щит от Багов ❓
Вопросы — нормально, подсказки — ускоряют. В геймдеве 80% времени — фиксы, 20% — креатив. Используй — и лети вперёд!  

**Что Далее?**  
- Вернись к урокам — примени подсказки.  
- Вопросы: [Unity Forum](https://forum.unity.com).  

Ты разобрал баги — теперь кодь без страха! Продолжай, фиксер. 🔧  

*Автор: [DGGelik](https://github.com/DGGelik). Дата: 18 октября 2025.*