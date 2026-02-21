## Част 11

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11.svg)

<em>11.0 Част 11 (с възможност за кликване)</em>

### Актуализирайте компонент

Коментарът в кода, който описва метода, гласи:

> „Извършване на актуализация на монтиран компонент. Извикват се методите componentWillReceiveProps и shouldComponentUpdate, след което (ако приемем, че актуализацията не е пропусната) се извикват останалите методи на жизнения цикъл на актуализацията и DOM представянето се актуализира. По подразбиране това имплементира алгоритъма за изобразяване и съгласуване на React. Сложните клиенти може да пожелаят да отменят това.“

Добре… звучи разумно.

Първото нещо, което проверяваме дали `props` (1) е променен, технически, методът `updateComponent` може да бъде извикан в два различни сценария, ако `setState` е извикан или `props` е променен. Ако `props` действително е променен, тогава ще бъде извикан методът на жизнения цикъл `componentWillReceiveProps`. След това React преизчислява `nextState` (2) въз основа на `pending state queue` (опашка от обекти с частично състояние, които сме задали преди, в нашия случай опашката ще бъде като [{message: "click state message"}]). Разбира се, в случай само на `props` състоянието на актуализацията ще бъде недокоснато.

Е, следващата стъпка задаваме `shouldUpdate` на стойност по подразбиране `true`(3). Ето защо, когато `shouldComponentUpdate` не е посочен, компонентът се актуализира по подразбиране. След това проверете дали не е `force update`. Както знаете, възможно е да извикате `forceUpdate` от компонент, за да го актуализирате, вместо да променяте `state` или `props`, но според официалните документи на React използването на този метод е лоша практика. Така че, в случай на принудителна актуализация, компонентът ще бъде актуализиран постоянно, в противен случай ще бъде извикан указаният от компонента метод `shouldComponentUpdate` и `shouldUpdate` ще бъде присвоен отново с неговата резултатна стойност. Ако се установи, че даден компонент не трябва да се актуализира, React все още трябва да зададе `props` и `state`, но да прекъсне останалата част от актуализацията.

### Добре, завършихме _част 11_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-A.svg)

<em>11.1 Част 11 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-B.svg)

<em>11.2 Част 11 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 11_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/11/part-11-C.svg)

<em>11.3 Основна стойност на част 11 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 12 >>](./Part-12.md)

[<< Към предишната страница: Част 10](./Part-10.md)

[Начало](../../README.md)
