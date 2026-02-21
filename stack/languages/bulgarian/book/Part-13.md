## Част 13

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13.svg)

<em>13.0 Част 13 (с възможност за кликване)</em>

### Компонент за получаване (следващ елемент, за да бъдем по-точни)

Е, чрез `ReactReconciler.receiveComponent` React всъщност извиква `receiveComponent` от `ReactDOMComponent` и предава следващия елемент там. Присвоете го отново на екземпляр на DOM компонент и извикайте метод за актуализиране. Методът `updateComponent` всъщност изпълнява две основни действия: актуализиране на свойствата на DOM и децата на DOM въз основа на `prev` и `next` props. Добре за нас, вече анализирахме метода `_updateDOMProperties` (`src\renderers\dom\shared\ReactDOMComponent.js#946`). Както си спомняте, този метод най-вече обработва свойства и атрибути на HTML елементи, изчислява стилове, обработва слушатели на събития и т.н. Това, което остава, е `_updateDOMChildren` (`src\renderers\dom\shared\ReactDOMComponent.js#1076`).

### Добре, завършихме _част 13_. Това беше кратко.)

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-A.svg)

<em>13.1 Част 13 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-B.svg)

<em>13.2 Част 13 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 13_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/13/part-13-C.svg)

<em>13.3 Основна стойност на част 13 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 14 >>](./Part-14.md)

[<< Към предишната страница: Част 12](./Part-12.md)

[Начало](../../README.md)
