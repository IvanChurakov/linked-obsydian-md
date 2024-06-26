## 💭 Суть патерна

**Фасад** — це структурний патерн проектування, який надає простий інтерфейс до складної системи класів, бібліотеки або фреймворку.

![[Pasted image 20240223122345.png | center]]

---
## 😟 Проблема

Вашому коду доводиться працювати з великою кількістю об’єктів певної складної бібліотеки чи фреймворка. Ви повинні самостійно ініціалізувати ці об’єкти, стежити за правильним порядком залежностей тощо.

В результаті бізнес-логіка ваших класів тісно переплітається з деталями реалізації сторонніх класів. Такий код досить складно розуміти та підтримувати.

---
## 😀 Рішення

Фасад — це простий інтерфейс для роботи зі складною підсистемою, яка містить безліч класів. Фасад може бути спрощеним відображенням системи, що не має 100% тієї функціональності, якої можна було б досягти, використовуючи складну підсистему безпосередньо. Разом з тим, він надає саме ті «фічі», які потрібні клієнтові, і приховує все інше.

Фасад корисний у тому випадку, якщо ви використовуєте якусь складну бібліотеку з безліччю рухомих частин, з яких вам потрібна тільки частина.

Наприклад, програма, що заливає в соціальні мережі відео з кошенятками, може використовувати професійну бібліотеку для стискання відео, але все, що потрібно клієнтському коду цієї програми, — це простий метод `encode(filename, format)`. Створивши клас з таким методом, ви реалізуєте свій перший фасад.

---
## 🏎️ Аналогія з життя

![[Pasted image 20240223122825.png | center]]
<center><i>Приклад замовлення через телефон</i></center>

Коли ви телефонуєте до магазину і робите замовлення, співробітник служби підтримки є вашим фасадом до всіх служб і відділів магазину. Він надає вам спрощений інтерфейс до системи створення замовлення, платіжної системи та відділу доставки.

---
## ➿ Структура

![[Pasted image 20240223122932.png | center]]

1. **Фасад** надає швидкий доступ до певної функціональності підсистеми. Він «знає», яким класам потрібно переадресувати запит, і які дані для цього потрібні.
2. **Додатковий фасад** можна ввести, щоб не захаращувати єдиний фасад різнорідною функціональністю. Він може використовуватися як клієнтом, так й іншими фасадами.
3. **Складна підсистема** має безліч різноманітних класів. Для того, щоб примусити усіх їх щось робити, потрібно знати подробиці влаштування підсистеми, порядок ініціалізації об’єктів та інші деталі.
    
    Класи підсистеми не знають про існування фасаду і працюють один з одним безпосередньо.
4. **Клієнт** використовує фасад замість безпосередньої роботи з об’єктами складної підсистеми.

---
## #️⃣ Псевдокод

У цьому прикладі **Фасад** спрощує роботу зі складним фреймворком конвертації відео.

![[Pasted image 20240223123238.png | center]]
<center><i>Приклад ізоляції множини залежностей в одному фасаді</i></center>

Замість безпосередньої роботи з дюжиною класів, фасад надає коду програми єдиний метод для конвертації відео, який сам подбає про те, щоб правильно налаштувати потрібні об’єкти фреймворку і отримати необхідний результат.

``` C#
// Класи складного стороннього фреймворку конвертації відео. Ми
// не контролюємо цей код, тому не можемо його спростити.

class VideoFile
// ...

class OggCompressionCodec
// ...

class MPEG4CompressionCodec
// ...

class CodecFactory
// ...

class BitrateReader
// ...

class AudioMixer
// ...

// Замість цього, ми створюємо Фасад — простий інтерфейс для
// роботи зі складним фреймворком. Фасад не має всієї
// функціональності фреймворку, але приховує його складність від
// клієнтів.
class VideoConverter is
    method convert(filename, format):File is
        file = new VideoFile(filename)
        sourceCodec = (new CodecFactory).extract(file)
        if (format == "mp4")
            destinationCodec = new MPEG4CompressionCodec()
        else
            destinationCodec = new OggCompressionCodec()
        buffer = BitrateReader.read(filename, sourceCodec)
        result = BitrateReader.convert(buffer, destinationCodec)
        result = (new AudioMixer()).fix(result)
        return new File(result)

// Програма не залежить від складного фреймворку конвертації
// відео. До речі, якщо ви раптом вирішите змінити фреймворк,
// вам потрібно буде переписати тільки клас фасаду.
class Application is
    method main() is
        convertor = new VideoConverter()
        mp4 = convertor.convert("funny-cats-video.ogg", "mp4")
        mp4.save()
```

---
## 💡 Застосування

### 🪲 Якщо вам потрібно надати простий або урізаний інтерфейс до складної підсистеми.

⚡ Часто підсистеми ускладнюються в міру розвитку програми. Застосування більшості патернів призводить до появи менших класів, але у великій кількості. Таку підсистему простіше використовувати повторно, налаштовуючи її кожен раз під конкретні потреби, але, разом з тим, використовувати таку підсистему без налаштовування важче. Фасад пропонує певний вид системи за замовчуванням, який влаштовує більшість клієнтів.

### 🪲 Якщо ви хочете розкласти підсистему на окремі рівні.

⚡ Використовуйте фасади для визначення точок входу на кожен рівень підсистеми. Якщо підсистеми залежать одна від одної, тоді залежність можна спростити, дозволивши підсистемам обмінюватися інформацією тільки через фасади.

Наприклад, візьмемо ту ж саму складну систему конвертації відео. Ви хочете розбити її на окремі шари для роботи з аудіо й відео. Можна спробувати створити фасад для кожної з цих частин і примусити класи аудіо та відео обробки спілкуватися один з одним через ці фасади, а не безпосередньо.

---
##  📃 Кроки реалізації

1. Визначте, чи можна створити більш простий інтерфейс, ніж той, який надає складна підсистема. Ви на правильному шляху, якщо цей інтерфейс позбавить клієнта від необхідності знати подробиці підсистеми.
2. Створіть клас фасаду, що реалізує цей інтерфейс. Він повинен переадресовувати виклики клієнта потрібним об’єктам підсистеми. Фасад повинен буде подбати про те, щоб правильно ініціалізувати об’єкти підсистеми.
3. Ви отримаєте максимум користі, якщо клієнт працюватиме тільки з фасадом. В такому випадку зміни в підсистемі стосуватимуться тільки коду фасаду, а клієнтський код залишиться робочим.
4. Якщо відповідальність фасаду стає розмитою, подумайте про введення додаткових фасадів.

---
## ⚖️ Переваги та недоліки

**Переваги:**
-  Ізолює клієнтів від компонентів складної підсистеми
**Недоліки:**
- Фасад ризикує стати [божественим об’єктом](https://refactoring.guru/uk/antipatterns/god-object), прив’язаним до всіх класів програми

---
## 🔁 Відносини з іншими патернами

- [[Facade (Фасад)|Фасад]] задає новий інтерфейс, тоді як [[Adapter (Адаптер)|Адаптер]] повторно використовує старий. _Адаптер_ обгортає тільки один клас, а _Фасад_ обгортає цілу підсистему. Крім того, _Адаптер_ дозволяє двом існуючим інтерфейсам працювати спільно, замість того, щоб визначити повністю новий.
- [[Abstract Factory (Абстрактна фабрика)|Абстрактна фабрика]] може бути використана замість [[Facade (Фасад)|Фасаду]] для того, щоб приховати платформо-залежні класи.
- [[Flyweight (Легковаговик)|Легковаговик]] показує, як створювати багато дрібних об’єктів, а [[Facade (Фасад)|Фасад]] показує, як створити один об’єкт, який відображає цілу підсистему.
- [[Mediator (Посередник)|Посередник]] та [[Facade (Фасад)|Фасад]] схожі тим, що намагаються організувати роботу багатьох існуючих класів.
    
    - _Фасад_ створює спрощений інтерфейс підсистеми, не вносячи в неї жодної додаткової функціональності. Сама підсистема не знає про існування _Фасаду_. Класи підсистеми спілкуються один з одним безпосередньо.
    - _Посередник_ централізує спілкування між компонентами системи. Компоненти системи знають тільки про існування _Посередника_, у них немає прямого доступу до інших компонентів.
	
- [[Facade (Фасад)|Фасад]] можна зробити [[Singleton (Одинак)|Одинаком]], оскільки зазвичай потрібен тільки один об’єкт-фасад.
- [[Facade (Фасад)|Фасад]] схожий на [[Proxy (Замісник)|Замісник]] тим, що замінює складну підсистему та може сам її ініціалізувати. Але, на відміну від _Фасаду_, _Замісник_ має такий самий інтерфейс, що і його службовий об’єкт, завдяки чому їх можна взаємозаміняти.