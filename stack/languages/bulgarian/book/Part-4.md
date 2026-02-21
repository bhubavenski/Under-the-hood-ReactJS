## Част 4

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4.svg)

<em>4.0 Част 4 (с възможност за кликване)</em>

### Детски монтаж

Полудяваш, нали? Нека продължим с изследването на метода `mount`.

Така че, ако `_tag` съдържа „сложен“ таг (1), като видео, формуляр, текстово поле и т.н., това ще изисква допълнително обвиване. Той добавя повече слушатели на събития за всяко медийно събитие, като „volumechange“ за `audio` тагове, или просто обгръща естественото поведение на тагове като `select`, `textarea` и т.н.
Има куп обвивки за елементи като този, като `ReactDOMSelect` и `ReactDOMTextarea` (вътре в папката src\renderers\dom\client\wrappers\). В нашия случай това е просто `div`, без допълнителна обработка.

### Проверка на реквизити

Следващият метод за валидиране се извиква само за да се увери, че вътрешните `props` са зададени правилно, в противен случай ще генерира грешки. Например, ако е зададен `props.dangerouslySetInnerHTML` (обикновено правим това, когато се опитваме да вмъкнем HTML от низ) и ключът на обекта `__html` е пропуснат, ще бъде изведена следващата грешка:

> `props.dangerouslySetInnerHTML` трябва да бъде във формата `{__html: ...}`. Моля, посетете https://fb.me/react-invariant-dangerously-set-inner-html за повече информация.

### Създайте HTML елемент

След това действителният HTML елемент ще бъде създаден (3) от `document.createElement`, което ще създаде истинския HTML `div` за нас. Преди работихме само с виртуално представяне, а сега можете да го видите за първи път.

### Добре, завършихме _част 4_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-A.svg)

<em>4.1 Част 4 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-B.svg)

<em>4.2 Част 4 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 4_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/4/part-4-C.svg)

<em>4.3 Основна стойност на част 4 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 5 >>](./Part-5.md)

[<< Към предишната страница: Част 3](./Part-3.md)

[Начало](../../README.md)
