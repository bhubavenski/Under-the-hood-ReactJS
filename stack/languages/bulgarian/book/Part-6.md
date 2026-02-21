## Част 6

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6.svg)

<em>6.0 Част 6 (с възможност за кликване)</em>

### Създайте първоначални деца

Изглежда, че самият елемент е завършен, така че сега можем да продължим с неговите деца. Две стъпки тук: децата трябва да бъдат монтирани (`this.mountChildren`)(1) и свързани с родителя (`DOMLazyTree.queueChild`)(2). Нека да преминем към монтирането на деца, защото очевидно е по-интересно.

Има отделен модул, наречен `ReactMultiChild` (`src\renderers\shared\stack\reconciler\ReactMultiChild.js`) за управление на деца. Добре, тогава нека проверим метода `mountChildren`. Той съдържа и две основни задачи. Първо, ние създаваме деца (използвайте `ReactChildReconciler` за това) и ги монтираме. Какви деца всъщност има тук? Това може да бъде обикновен HTML таг или друг персонализиран компонент. За да работим с HTML, трябва да инстанцираме `ReactDOMComponent`, а за персонализиран компонент - `ReactCompositeComponent`. Монтажният поток отново зависи от типа на детето.

### Още веднъж

Ако все още четете това, вероятно е време да изясните и прегледате цялостния процес още веднъж. Нека си дадем почивка и да си припомним последователността от обекти.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/overall-mounting-scheme.svg)

<em>6.1 Обща схема на монтиране (с възможност за щракване)</em>

1. React създава `ReactCompositeComponent` за всеки от вашите персонализирани компоненти (с кукички за жизнения цикъл на компонента като `componentWillMount` и т.н.) и го монтира.

2. По време на монтирането първо ще бъде създаден екземпляр на вашия потребителски компонент (извикан `constructor`).

3. След това се извиква методът му за изобразяване (за прост пример, изобразяването връща `div`) и `React.createElement` създава React елементите. Може да бъде извикан директно или след анализиране на JSX от Babel и замяна на тагове във вашия рендер. Но това не е точно това, от което се нуждаем, вижте какво следва по-долу.

4. Имаме нужда от DOM компонент за нашия `div`. И така, по време на процес на инстанциране, ние създаваме екземпляри на `ReactDOMComponent` от елементите-обекти (споменати по-горе).

5. След това трябва да монтираме DOM компонента. Това всъщност означава, че създаваме DOM елементите и присвояваме слушатели на събития и т.н.

6. След това обработваме първоначалните деца на нашия DOM компонент. Ние създаваме техни екземпляри и също ги монтираме. В зависимост от това какъв е всеки елемент от децата, персонализиран компонент или просто HTML таг, ние се връщаме съответно към стъпка 1) или стъпка 5). И след това отново за всички вложени елементи.

Това е. Доста е просто, както можете да видите.

И така, монтажът е основно завършен. Поставете в опашка метода `componentDidMount`! страхотна работа

### Добре, завършихме _Част 6_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-A.svg)

<em>6.2 Част 6 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-B.svg)

<em>6.3 Част 6 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 6_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/6/part-6-C.svg)

<em>6.4 Основна стойност на част 6 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 7 >>](./Part-7.md)

[<< Към предишната страница: Част 5](./Part-5.md)

[Начало](../../README.md)
