## Част 0

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0.svg)

<em>0.0 Част 0 (с възможност за кликване)</em>

### ReactDOM.render

Добре, нека започнем с извикване на ReactDOM.render.

Входната точка е ReactDom.render. Нашето приложение започва да рендира в DOM от тук. Създадох прост компонент `<ExampleApplication/>` за по-лесно отстраняване на грешки. И така, първото нещо, което се случва е, че **JSX ще бъде трансформиран в React елементи**. Те са доста прости, почти обикновени обекти с проста структура. Те просто представляват това, което е върнато от рендирането на компонента, нищо повече. Някои полета трябва вече да са ви познати като подпори, ключ и реф. Типът свойство се отнася до обекта за маркиране, описан от JSX. Така че в нашия случай това е клас `ExampleApplication`, но също така може да бъде просто низ `button` за етикет на бутон и т.н. Освен това, по време на създаването на React елемент, React ще обедини `defaultProps` с `props` (ако са посочени) и ще потвърди `propTypes`.

Проверете изходния код за повече подробности: `src\isomorphic\classic\element\ReactElement.js`

### ReactMount

Можете да видите модула, наречен `ReactMount` (01). Той съдържа логиката на монтиране на компоненти. Всъщност няма никаква логика вътре в `ReactDOM`, това е просто интерфейс за работа с `ReactMount`, така че когато извиквате `ReactDOM.render`, вие технически извиквате `ReactMount.render`. Какво е всичко това монтиране?

> Монтирането е процес на инициализиране на React компонент чрез създаване на негови представителни DOM елементи и вмъкването им в предоставен `container`.

Поне коментарът от кода го описва по този начин. Е, какво всъщност означава това? Добре, представете си следващата трансформация:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-small.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-small.svg)

<em>0.1 JSX към HTML (с възможност за кликване)</em>

React трябва да **преобразува описанието на вашия компонент(и) в HTML**, за да го постави в документ. Как да стигнем до там? Точно така, той трябва да обработва всички **реквизити, слушатели на събития, вложени компоненти** и логика. Необходимо е да се гранулира вашето описание от високо ниво (компоненти) до данни от наистина ниско ниво (HTML), които могат да бъдат поставени в уеб страница. Това е всичко, което всъщност представлява монтажът.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-big.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/mounting-scheme-1-big.svg)

<em>0.1 JSX към HTML, разширен (с възможност за кликване)</em>

Добре, да продължим. Но… време е за интересен факт! Да, нека добавим някои интересни неща по време на нашето пътуване, за да се "забавляваме" повече.

> Интересен факт: Уверете се, че скролът се наблюдава (02)

> Странно нещо, по време на първото изобразяване на основен компонент, React инициализира слушатели на превъртане и кешира стойности на превъртане, така че кодът на приложението да има достъп до тях, без да задейства преформатиране. Всъщност, поради различните имплементации на рендиране на браузъра, някои DOM стойности не са статични, те се изчисляват всеки път, когато ги използвате в кода. Разбира се, това се отразява на производителността. Всъщност това е само за по-стари браузъри, които не поддържат `pageX` и `pageY`. Reacts се опитва да оптимизира и това. хубаво. Както можете да видите, създаването на бърз инструмент изисква използването на куп техники, тази с превъртане е добър пример.

### Създаване на React компонент

Вижте схемата, има създаване на инстанция по номер (03). Е, твърде рано е да се създаде екземпляр на `<ExampleApplication />` тук. Всъщност ние създаваме `TopLevelWrapper` (вътрешен React клас). Нека първо да разгледаме следващата схема.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/jsx-to-vdom.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/jsx-to-vdom.svg)

<em>0.3 JSX към VDOM (с възможност за кликване)</em>

Можете да видите три фази, JSX чрез React елементи ще бъдат преобразувани в един от вътрешните типове компоненти на React: `ReactCompositeComponent` (за нашите собствени компоненти), `ReactDOMComponent` (за HTML тагове) и `ReactDOMTextComponent` (за текстови възли). Ще пропуснем `ReactDOMTextComponent` и ще се съсредоточим върху първите две.

Вътрешни компоненти? Е, това е интересно. Вече сте чували за **Virtual DOM**, нали? Виртуалният DOM е вид DOM представяне, което се използва от React, за да не се докосва DOM директно по време на разлики изчисления и т.н. Прави React бързо! Но всъщност няма файлове или класове в изходния код на React, наречен „Virtual DOM“. Това е смешно, нали? Е, защото V-DOM е просто концепция, подход за това как да работите с истинския DOM. И така, някои хора казват, че елементите на V-DOM се отнасят до елементи на React, но според мен това не е съвсем вярно. Мисля, че Virtual DOM се отнася до тези три класа: `ReactCompositeComponent`, `ReactDOMComponent`, `ReactDOMTextComponent`. И ще видите по-късно защо.

Добре, нека приключим с нашето инстанциране тук. Ще създадем екземпляр на `ReactCompositeComponent`, но всъщност не е защото сме поставили `<ExampleApplication/>` в `ReactDOM.render`. React винаги започва изобразяването на дървото на компонент от `TopLevelWrapper`. Това е почти неактивна обвивка, нейният `render` (метод за изобразяване на компонент) по-късно ще върне `<ExampleApplication />`, това е.

```javascript
//src\renderers\dom\client\ReactMount.js#277
TopLevelWrapper.prototype.render = function () {
  return this.props.child;
};
```

И така, само `TopLevelWrapper` е създадено, нищо повече за сега. Придвижване напред. Но... първо, един интересен факт!

> Интересен факт: Валидирайте DOM Nesting

> Почти всеки път, когато вложените компоненти се рендират, те се валидират от специален модул за валидиране на HTML, наречен `validateDOMNesting`. Валидирането на влагането на DOM означава проверка на йерархията на етикетите `child -> parent`. Например, ако родителският таг е `<select>`, дъщерният таг трябва да бъде само едно от следните: `option`, `optgroup` или `#text`. Тези правила всъщност са дефинирани в https://html.spec.whatwg.org/multipage/syntax.html#parsing-main-inselect. Вероятно вече сте виждали този модул да работи, той попълва грешки като:
> <em> <div> не може да се появи като наследник на <p> </em>.

### Добре, завършихме _част 0_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-A.svg)

<em>0.4 Част 0 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-B.svg)

<em>0.5 Част 0 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 0_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/0/part-0-C.svg)

<em>0,6 Част 0 основна стойност (може да се кликне)</em>

И тогава сме готови!

[Към следващата страница: Част 1 >>](./Part-1.md)

[<< Към предишната страница: Въведение](./Intro.md)

[Начало](../../README.md)
