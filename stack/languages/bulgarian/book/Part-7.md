## Част 7

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7.svg)

<em>7.0 Част 7 (с възможност за кликване)</em>

### Обратно в началото

След монтирането като резултат от изпълнението на метода, имаме HTML елементи, които са готови за поставяне в документ. Всъщност `markup` (1) се генерира, но `mountComponent`, въпреки името му, всъщност не е HTML маркиране. Това е структура от данни с полета `children`, `node` (действителни DOM възли) и т.н. Но имаме нашия HTML елемент, който да поставим в контейнера (този, който е определен като контейнер в извикването `ReactDOM.render`). Докато го добавя в DOM, React ще изтрие всичко, което е било там преди. `DOMLazyTree`(2) е клас utils, който извършва някои операции с дървовидни структури от данни, които всъщност правим по време на работа с DOM.

Последното нещо е `parentNode.insertBefore(tree.node)`(3), където `parentNode` е възелът на контейнера `div`, а `tree.node` всъщност е нашият `ExampleAppliication` div възел. Хубаво, HTML елементите, които бяха създадени по време на монтирането, най-накрая бяха вмъкнати в документа.

И така, това е? Не точно. Както си спомняте, обаждането `mount` беше обвито в транзакция. Това означава, че трябва да го затворим. Нека проверим нашия списък с `close` обвивки. Най-вече трябва да възстановим някои заключени действия `ReactInputSelection.restoreSelection()`, `ReactBrowserEventEmitter.setEnabled(previouslyEnabled)`, но също така ще уведомим всички обратни извиквания `this.reactMountReady.notifyAll`(4), които поставихме в опашката `transaction.reactMountReady` преди. Един от тях е любимият ни `componentDidMount`, който ще бъде задействан точно от обвивката `close`.

Сега имате ясна представа какво всъщност означава „компонентът е монтиран“. наздраве!

### Още една транзакция за затваряне

Е, всъщност тази сделка не беше само една. Забравихме още един, който беше използван за приключване на обаждането `ReactMount.batchedMountComponentIntoNode`. Нека и него затворим.

Тук проверяваме обвивката `ReactUpdates.flushBatchedUpdates`(5), която ще обработва `dirtyComponents`. Звучи интересно, нали? Е, добри или лоши новини. Току-що направихме първия си монтаж, така че все още няма мръсни компоненти. Това означава, че това е празен разговор. Така че можем да затворим и тази транзакция и да кажем, че актуализациите на стратегията за групиране са готови.

### Добре, завършихме _част 7_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-A.svg)

<em>7.1 Част 7 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-B.svg)

<em>7.2 Част 7 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 7_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/part-7-C.svg)

<em>7.3 Основна стойност на част 7 (с възможност за кликване)</em>

И тогава сме готови! Всъщност приключихме с монтажа. Нека го видим по-долу!

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/7/mounting-parts-C.svg)

<em>7.4 Монтиране (с възможност за кликване)</em>

[Към следващата страница: Част 8 >>](./Part-8.md)

[<< Към предишната страница: Част 6](./Part-6.md)

[Начало](../../README.md)
