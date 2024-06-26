## 💭 Суть патерна

**Команда** — це поведінковий патерн проектування, який перетворює запити на об’єкти, дозволяючи передавати їх як аргументи під час виклику методів, ставити запити в чергу, логувати їх, а також підтримувати скасування операцій.
![[Pasted image 20240210210326.png | center]]

---
## 😟 Проблема

Уявіть, що ви працюєте над програмою текстового редактора. Якраз підійшов час розробки панелі керування. Ви створили клас гарних `Кнопок` і хочете використовувати його для всіх кнопок програми, починаючи з панелі керування та закінчуючи звичайними кнопками в діалогах.
![[Pasted image 20240210210414.png | center]]
<center><i>Всі кнопки програми успадковані від одного класу</i></center>

Усі ці кнопки, хоч і виглядають схоже, але виконують різні команди. Виникає запитання: куди розмістити код обробників кліків по цих кнопках? Найпростіше рішення — це створити підкласи для кожної кнопки та перевизначити в них методи дії для різних завдань.
![[Pasted image 20240210210500.png | center]]
<center><i>Безліч підкласів кнопок</i></center>

Але скоро стало зрозуміло, що такий підхід нікуди не годиться. По-перше, з’являється дуже багато підкласів. По-друге, код кнопок, який відноситься до графічного інтерфейсу, починає залежати від класів бізнес-логіки, яка досить часто змінюється.
![[Pasted image 20240210210545.png | center]]
<center><i>Кілька класів дублюють одну і ту саму функціональність</i></center>

Проте, найгірше ще попереду, адже деякі операції, на кшталт «зберегти», можна викликати з декількох місць: натиснувши кнопку на панелі керування, викликавши контекстне меню або натиснувши клавіші `Ctrl+S`. Коли в програмі були тільки кнопки, код збереження був тільки у підкласі `SaveButton`. Але тепер його доведеться продублювати ще в два класи.

---
## 😀 Рішення

Хороші програми зазвичай структурують у вигляді шарів. Найпоширеніший приклад — це шари користувацького інтерфейсу та бізнес-логіки. Перший лише малює гарне зображення для користувача, але коли потрібно зробити щось важливе, інтерфейс користувача «просить» шар бізнес-логіки зайнятися цим.

У дійсності це виглядає так: один з об’єктів інтерфейсу користувача викликає метод одного з об’єктів бізнес-логіки, передаючи до нього якісь параметри.
![[Pasted image 20240210210825.png | center]]
<center><i>Прямий доступ з UI до бізнес-логіки</i></center>

Патерн Команда пропонує більше не надсилати такі виклики безпосередньо. Замість цього кожен виклик, що відрізняється від інших, слід звернути у власний клас з єдиним методом, який і здійснюватиме виклик. Такий об’єкт зветься _командою_.

До об’єкта інтерфейсу можна буде прив’язати об’єкт команди, який знає, кому і в якому вигляді слід відправляти запити. Коли об’єкт інтерфейсу буде готовий передати запит, він викличе метод команди, а та — подбає про все інше.
![[Pasted image 20240210211110.png | center]]
<center><i>Доступ з UI до бізнес-логіки через команду</i></center>

Класи команд можна об’єднати під загальним інтерфейсом, що має єдиний метод запуску команди. Після цього одні й ті самі відправники зможуть працювати з різними командами, не прив’язуючись до їхніх класів. Навіть більше, команди можна буде взаємозаміняти «на льоту», змінюючи підсумкову поведінку відправників.

Параметри, з якими повинен бути викликаний метод об’єкта одержувача, можна заздалегідь зберегти в полях об’єкта-команди. Завдяки цьому, об’єкти, які надсилають запити, можуть не турбуватися про те, щоб зібрати необхідні дані для одержувача. Навіть більше, вони тепер взагалі не знають, хто буде одержувачем запиту. Вся ця інформація прихована всередині команди.
![[Pasted image 20240210211423.png | center]]
<center><i>Класи UI делегують роботу командам</i></center>

Після застосування Команди в нашому прикладі з текстовим редактором вам більше не потрібно буде створювати безліч підкласів кнопок для різних дій. Буде достатньо одного класу з полем для зберігання об’єкта команди.

Використовуючи загальний інтерфейс команд, об’єкти кнопок посилатимуться на об’єкти команд різних типів. При натисканні кнопки делегуватимуть роботу командам, а команди — перенаправляти виклики тим чи іншим об’єктам бізнес-логіки.

Так само можна вчинити і з контекстним меню, і з гарячими клавішами. Вони будуть прив’язані до тих самих об’єктів команд, що і кнопки, позбавляючи класи від дублювання.

Таким чином, команди стануть гнучким прошарком між користувацьким інтерфейсом та бізнес-логікою. І це лише невелика частина тієї користі, яку може принести патерн Команда!

---
## 🏎️ Аналогія з життя

![[Pasted image 20240210211920.png | center]]
<center><i>Приклад замовлення в ресторані</i></center>

Ви заходите в ресторан і сідаєте біля вікна. До вас підходить ввічливий офіціант і приймає замовлення, записуючи всі побажання в блокнот.

Закінчивши, він поспішає на кухню, вириває аркуш з блокнота та клеїть його на стіну. Далі лист опиняється в руках кухаря, який читає замовлення і готує описану страву.

У цьому прикладі ви є _відправником_, офіціант з блокнотом — _командою_, а кухар — _отримувачем_. Як і в самому патерні, ви не стикаєтесь з кухарем безпосередньо. Замість цього ви відправляєте замовлення офіціантом, який самостійно «налаштовує» кухаря на роботу. З іншого боку, кухар не знає, хто конкретно надіслав йому замовлення. Але йому це байдуже, бо вся необхідна інформація є в листі замовлення.

---
## ➿ Структура

![[Pasted image 20240210212321.png | center]]
1. **Відправник** зберігає посилання на об’єкт команди та звертається до нього, коли потрібно виконати якусь дію. Відправник працює з командами тільки через їхній загальний інтерфейс. Він не знає, яку конкретно команду використовує, оскільки отримує готовий об’єкт команди від клієнта.
2. **Команда** описує інтерфейс, спільний для всіх конкретних команд. Зазвичай тут описується лише один метод запуску команди.
3. **Конкретні команди** реалізують різні запити, дотримуючись загального інтерфейсу команд. Як правило, команда не робить всю роботу самостійно, а лише передає виклик одержувачу, яким виступає один з об’єктів бізнес-логіки. Параметри, з якими команда звертається до одержувача, необхідно зберігати у вигляді полів. У більшості випадків об’єкти команд можна зробити незмінними, передаючи у них всі необхідні параметри тільки через конструктор.
4. **Одержувач** містить бізнес-логіку програми. У цій ролі може виступати практично будь-який об’єкт. Зазвичай, команди перенаправляють виклики одержувачам, але іноді, щоб спростити програму, ви можете позбутися від одержувачів, «зливши» їхній код у класи команд.
5. **Клієнт** створює об’єкти конкретних команд, передаючи до них усі необхідні параметри, серед яких можуть бути і посилання на об’єкти одержувачів. Після цього клієнт зв’язує об’єкти відправників зі створеними командами.

---
## #️⃣ Псевдокод

У цьому прикладі патерн **Команда** використовується для ведення історії виконаних операцій, дозволяючи скасовувати їх за потреби.
![[Pasted image 20240210213433.png | center]]
<center><i>Приклад реалізації скасування у текстовому редакторі</i></center>

Команди, які змінюють стан редактора (наприклад, команда вставки тексту з буфера обміну), зберігають копію стану редактора перед виконанням дії. Копії виконаних команд розміщуються в історії команд, звідки вони можуть бути доставлені, якщо потрібно буде скасувати виконану операцію.

Класи елементів інтерфейсу, історії команд та інші не залежать від конкретних класів команд, оскільки працюють з ними через загальний інтерфейс. Це дозволяє додавати до програми нові команди, не змінюючи наявний код.

``` C#
// Абстрактна команда задає загальний інтерфейс для конкретних
// класів команд, а також містить реалізацію базової поведінки
// скасування операції.
abstract class Command is
    protected field app: Application
    protected field editor: Editor
    protected field backup: text

    constructor Command(app: Application, editor: Editor) is
        this.app = app
        this.editor = editor

    // Зберігаємо стан редактора.
    method saveBackup() is
        backup = editor.text

    // Відновлюємо стан редактора.
    method undo() is
        editor.text = backup

    // Головний метод команди залишається абстрактним, щоб кожна
    // конкретна команда визначила його по-своєму. Метод повинен
    // повернути true або false, залежно від того, чи змінила
    // команда стан редактора, а отже, чи потрібно її зберігати
    // в історії.
    abstract method execute()

// Конкретні команди.
class CopyCommand extends Command is
    // Команда копіювання не записується до історії, бо вона не
    // змінює стан редактора.
    method execute() is
        app.clipboard = editor.getSelection()
        return false

class CutCommand extends Command is
    // Команди, що змінюють стан редактора, зберігають стан
    // редактора перед своєю дією і сигналізують про зміну,
    // повертаючи true.
    method execute() is
        saveBackup()
        app.clipboard = editor.getSelection()
        editor.deleteSelection()
        return true

class PasteCommand extends Command is
    method execute() is
        saveBackup()
        editor.replaceSelection(app.clipboard)
        return true

// Відміна — це також команда.
class UndoCommand extends Command is
    method execute() is
        app.undo()
        return false

// Глобальна історія команд — це стек.
class CommandHistory is
    private field history: array of Command

    // Той, що зайшов останнім...
    method push(c: Command) is
        // Додати команду в кінець масиву-історії.

    // ...виходить першим.
    method pop():Command is
        // Дістати останню команду з масиву-історії.

// Клас редактора містить безпосередні операції над текстом. Він
// відіграє роль одержувача — команди делегують йому свої дії.
class Editor is
    field text: string

    method getSelection() is
        // Повернути вибраний текст.

    method deleteSelection() is
        // Видалити вибраний текст.

    method replaceSelection(text) is
        // Вкласти текст з буфера обміну в поточній позиції.

// Клас програми налаштовує об'єкти для спільної роботи. Він
// виступає у ролі відправника — створює команди, щоб виконати
// якісь дії.
class Application is
    field clipboard: string
    field editors: array of Editors
    field activeEditor: Editor
    field history: CommandHistory

    // Код, що прив'язує команди до елементів інтерфейсу, може
    // виглядати приблизно так.
    method createUI() is
        // ...
        copy = function() {executeCommand(
            new CopyCommand(this, activeEditor)) }
        copyButton.setCommand(copy)
        shortcuts.onKeyPress("Ctrl+C", copy)

        cut = function() { executeCommand(
            new CutCommand(this, activeEditor)) }
        cutButton.setCommand(cut)
        shortcuts.onKeyPress("Ctrl+X", cut)

        paste = function() { executeCommand(
            new PasteCommand(this, activeEditor)) }
        pasteButton.setCommand(paste)
        shortcuts.onKeyPress("Ctrl+V", paste)

        undo = function() { executeCommand(
            new UndoCommand(this, activeEditor)) }
        undoButton.setCommand(undo)
        shortcuts.onKeyPress("Ctrl+Z", undo)

    // Запускаємо команду й перевіряємо, чи потрібно додати її
    // до історії.
    method executeCommand(command) is
        if (command.execute())
            history.push(command)

    // Беремо останню команду з історії та змушуємо її все
    // скасувати. Ми не знаємо конкретний тип команди, але це і
    // не важливо, оскільки кожна команда знає, як скасувати
    // свою дію.
    method undo() is
        command = history.pop()
        if (command != null)
            command.undo()
```

---
## 💡 Застосування

### 🪲 Якщо ви хочете параметризувати об’єкти виконуваною дією.

⚡ Команда перетворює операції на об’єкти, а об’єкти, у свою чергу, можна передавати, зберігати та взаємозаміняти всередині інших об’єктів.

Скажімо, ви розробляєте бібліотеки графічного меню і хочете, щоб користувачі могли використовувати меню в різних програмах, не змінюючи кожного разу код ваших класів. Застосувавши патерн, користувачам не доведеться змінювати класи меню, замість цього вони будуть конфігурувати об’єкти меню різними командами.

### 🪲 Якщо ви хочете поставити операції в чергу, виконувати їх за розкладом або передавати мережею. 

⚡ Як і будь-які інші об’єкти, команди можна серіалізувати, тобто перетворити на рядок, щоб потім зберегти у файл або базу даних. Потім в будь-який зручний момент його можна дістати назад, знову перетворити на об’єкт команди та виконати. Так само команди можна передавати мережею, логувати або виконувати на віддаленому сервері.

### 🪲 Якщо вам потрібна операція скасування.

⚡Головна річ, яка потрібна для того, щоб мати можливість скасовувати операції — це зберігання історії. Серед багатьох способів реалізації цієї можливості патерн Команда є, мабуть, найпопулярнішим.

Історія команд виглядає як стек, до якого потрапляють усі виконані об’єкти команд. Кожна команда перед виконанням операції зберігає поточний стан об’єкта, з яким вона працюватиме. Після виконання операції копія команди потрапляє до стеку історії, продовжуючи нести у собі збережений стан об’єкта. Якщо знадобиться скасування, програма візьме останню команду з історії та відновить збережений у ній стан.

Цей спосіб має дві особливості. По-перше, точний стан об’єктів не дуже просто зберегти, адже його частина може бути приватною. Вирішити це можна за допомогою патерна [[Memento (Знімок)|Знімок]].

По-друге, копії стану можуть займати досить багато оперативної пам’яті. Тому іноді можна вдатися до альтернативної реалізації, тобто замість відновлення старого стану, команда виконає зворотню дію. Недолік цього способу у складності (іноді неможливості) реалізації зворотньої дії.

---
##  📃 Кроки реалізації

1. Створіть загальний інтерфейс команд і визначте в ньому метод запуску.

2. Один за одним створіть класи конкретних команд. У кожному класі має бути поле для зберігання посилання на один або декілька об’єктів-одержувачів, яким команда перенаправлятиме основну роботу.
    
    Крім цього, команда повинна мати поля для зберігання параметрів, потрібних під час виклику методів одержувача. Значення всіх цих полів команда повинна отримувати через конструктор.
    
    І, нарешті, реалізуйте основний метод команди, викликаючи в ньому ті чи інші методи одержувача.
    
3. Додайте до класів відправників поля для зберігання команд. Зазвичай об’єкти-відправники приймають готові об’єкти команд ззовні — через конструктор або через сетер поля команди.

4. Змініть основний код відправників так, щоб вони делегували виконання дії команді.

5. Порядок ініціалізації об’єктів повинен виглядати так:
	
	- Створюємо об’єкти одержувачів.
	- Створюємо об’єкти команд, зв’язавши їх з одержувачами.
	- Створюємо об’єкти відправників, зв’язавши їх з командами.

---
## ⚖️ Переваги та недоліки

**Переваги:**
- Прибирає пряму залежність між об’єктами, що викликають операції, та об’єктами, які їх безпосередньо виконують.
- Дозволяє реалізувати просте скасування і повтор операцій.
- Дозволяє реалізувати відкладений запуск операцій.
- Дозволяє збирати складні команди з простих.
- Реалізує _принцип відкритості/закритості_.
**Недоліки:**
- Ускладнює код програми внаслідок введення великої кількості додаткових класів.

---
## 🔁 Відносини з іншими патернами

- [[Chain of Responsibility (Ланцюжок обов'язків)|Ланцюжок обов’язків]], [[Command (Команда)|Команда]], [[Mediator (Посередник)|Посередник]] та [[Observer (Спостерігач)|Спостерігач]] показують різні способи роботи тих, хто надсилає запити, та тих, хто їх отримує:
    - _Ланцюжок обов’язків_ передає запит послідовно через ланцюжок потенційних отримувачів, очікуючи, що один з них обробить запит.
    - _Команда_ встановлює непрямий односторонній зв’язок від відправників до одержувачів.
    - _Посередник_ прибирає прямий зв’язок між відправниками та одержувачами, змушуючи їх спілкуватися опосередковано, через себе.
    - _Спостерігач_ передає запит одночасно всім зацікавленим одержувачам, але дозволяє їм динамічно підписуватися або відписуватися від таких повідомлень.

- Обробники в [[Chain of Responsibility (Ланцюжок обов'язків)|Ланцюжкові обов’язків]] можуть бути виконані у вигляді [[Command (Команда)|Команд]]. В цьому випадку роль запиту відіграє контекст команд, який послідовно подається до кожної команди у ланцюгу.
    
    Але є й інший підхід, в якому сам запит є _Командою_, надісланою ланцюжком об’єктів. У цьому випадку одна і та сама операція може бути застосована до багатьох різних контекстів, представлених у вигляді ланцюжка.
    
- [[Command (Команда)|Команду]] та [[Memento (Знімок)|Знімок]] можна використовувати спільно для реалізації скасування операцій. У цьому випадку об’єкти команд відповідатимуть за виконання дії над об’єктом, а знімки зберігатимуть резервну копію стану цього об’єкта, зроблену перед запуском команди.
    
- [[Command (Команда)|Команда]] та [[Strategy (Стратегія)|Стратегія]] схожі за принципом, але відрізняються масштабом та застосуванням:
    - _Команду_ використовують для перетворення будь-яких різнорідних дій на об’єкти. Параметри операції перетворюються на поля об’єкта. Цей об’єкт тепер можна логувати, зберігати в історії для скасування, передавати у зовнішні сервіси тощо.
    - З іншого боку, _Стратегія_ описує різні способи того, як зробити одну і ту саму дію, дозволяючи замінювати ці способи в якомусь об’єкті контексту прямо під час виконання програми.

- Якщо [[Command (Команда)|Команду]] потрібно копіювати перед вставкою в історію виконаних команд, вам може допомогти [[Prototype (Прототип)|Прототип]].
    
- [[Visitor (Відвідувач)|Відвідувач]] можна розглядати як розширений аналог [[Command (Команда)|Команди]], що здатен працювати відразу з декількома видами одержувачів.