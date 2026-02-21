## Част 10

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10.svg)

<em>10.0 Част 10 (с възможност за кликване)</em>

### Мръсни компоненти

Както можете да видите, React преминава през `dirtyComponents`(1) и извиква `ReactUpdates.runBatchedUpdates`(2) чрез транзакция! Транзакция? Новият, но защо? Да видим.

Типът транзакция е `ReactUpdatesFlushTransaction` и вече споменахме, че трябва да проверим `wrappers`, за да разберем какво всъщност прави транзакцията. Малък съвет от коментара на кода:

> ‘Обвивките на ReactUpdatesFlushTransaction ще изчистят масива dirtyComponents и ще изпълнят всички актуализации, поставени в опашката от манипулатори, готови за монтиране (т.е. componentDidUpdate)’

Но така или иначе трябва да го докажем. Има две обвивки `NESTED_UPDATES` и `UPDATE_QUEUEING`. Във фаза `initialize` ние съхраняваме `dirtyComponentsLength` (3) и, както можете да проверите на `close`, React сравнява, може би по време на актуализациите е променен голям брой мръсни компоненти, така че очевидно е необходимо да стартирате `flushBatchedUpdates` още веднъж. Виждате ли, няма магия, всичко е доста просто.

Е.. един вълшебен момент наистина присъства. `ReactUpdatesFlushTransaction` отменя метода `Transaction.perform`, защото... всъщност изисква поведение от `ReactReconcileTransaction` (транзакцията се използва по време на монтиране и позволява да се запази състоянието на приложението безопасно). И така, в метода `ReactUpdatesFlushTransaction.perform` се използва и `ReactReconcileTransaction`, така че методът на транзакция всъщност се обвива още веднъж.

И така, технически изглежда така:

```javascript
[NESTED_UPDATES, UPDATE_QUEUEING].initialize()
[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].initialize()

method -> ReactUpdates.runBatchedUpdates

[SELECTION_RESTORATION, EVENT_SUPPRESSION, ON_DOM_READY_QUEUEING].close()
[NESTED_UPDATES, UPDATE_QUEUEING].close()
```

Накрая ще се върнем към транзакцията, за да проверим отново как помага да завършим работата на метода, но сега нека видим подробности за `ReactUpdates.runBatchedUpdates`(2) (`\src\renderers\shared\stack\reconciler\ReactUpdates.js#125`)

Първото нещо, което трябва да направим от самото начало - да сортираме `dirtyComponets` масив (4). Как да сортирам? Чрез `mount order` (цяло число е зададено на компонент, когато екземплярът е монтиран), това означава, че родителите (те са монтирани първи) ще бъдат актуализирани първи, децата следват и т.н.
Следващата стъпка, увеличаваме `updateBatchNumber`, това е нещо като ID за текущо съгласуване. Според коментар в кода:

> „Всички актуализации, поставени в опашка по време на съгласуване, трябва да се извършат след целия този пакет. В противен случай, ако dirtyComponents е [A, B], където A има деца B и C, B може да се актуализира два пъти в една партида, ако рендирането на C постави актуализация на B (тъй като B вече би се актуализирал, трябва да го пропуснем и единственият начин, по който можем да знаем да го направим, е като проверим брояча на партидите).“

Всъщност помага да се избегнат двойни актуализации за едни и същи компоненти.

Браво, най-накрая преминаваме през `dirtyComponents` и предаваме всеки компонент на `ReactReconciler.performUpdateIfNecessary` (5), където всъщност методът `performUpdateIfNecessary` ще бъде извикан от екземпляр `ReactCompositeComponent`, така че преминете отново към кода `ReactCompositeComponent` и неговия метод `updateComponent`. Тук можем да намерим нещо интересно за нас, така че нека се потопим по-дълбоко.

### Добре, завършихме _част 10_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-A.svg)

<em>10.1 Част 10 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-B.svg)

<em>10.2 Част 10 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 10_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/10/part-10-C.svg)

<em>10.3 Основна стойност на част 10 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 11 >>](./Part-11.md)

[<< Към предишната страница: Част 9](./Part-9.md)

[Начало](../../README.md)
