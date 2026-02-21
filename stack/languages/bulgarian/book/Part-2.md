## Част 2

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2.svg)

<em>2.0 Част 2 (с възможност за кликване)</em>

### Още една транзакция

Този път е `ReactReconcileTransaction`. Както вече знаете, основното нещо, което е интересно за нас, са обвивките на транзакциите. Има три обвивки:

```javascript
//\src\renderers\dom\client\ReactReconcileTransaction.js#89
var TRANSACTION_WRAPPERS = [
  SELECTION_RESTORATION,
  EVENT_SUPPRESSION,
  ON_DOM_READY_QUEUEING,
];
```

Както виждаме, тези обвивки се използват най-вече за **запазване на действителното състояние**, заключване на някои променливи стойности преди извикване на метод и освобождаването им след това. Така че React гарантира, че например диапазонът за избор (текущо избраното въвеждане на текст) не е нарушен от извършването на транзакцията (избиране на `initialize` и възстановяване на `close`). Освен това той потиска събития (замъгляване/фокус), които биха могли да бъдат изпратени по невнимание поради манипулации на DOM на високо ниво (като временно премахване на въвеждане на текст от DOM), така че **деактивира `ReactBrowserEventEmitter`** на `initialize` и активира на `close`.

Е, наистина сме близо до стартирането на монтирането на компонента, което ще ни върне маркиране, готово за поставяне в DOM. Всъщност `ReactReconciler.mountComponent` е просто обвивка, или по-правилно е да се каже „посредник“. Той делегира метод за монтиране на компонентни модули. Това е важен момент, така че нека подчертаем:

> Модулът `ReactReconciler` винаги се извиква в случаите, когато изпълнението на някаква логика **зависи от платформата**, като този точен случай. Монтирането е различно за всяка платформа, така че „главният модул“ говори с `ReactReconciler` и `ReactReconciler` знае какво да прави по-нататък.

Добре, нека да преминем към метода на компонента `mountComponent`. Вероятно това е методът, за който вече сте чували. Той инициализира компонента, изобразява маркиране и регистрира слушатели на събития. Виждате ли, дълъг път и най-накрая виждаме обаждане за монтиране на компонент. След като извикаме mount, трябва да получим действителни HTML елементи, които могат да бъдат поставени в документа.

### Добре, завършихме _част 2_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-A.svg)

<em>2.1 Част 2 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-B.svg)

<em>2.2 Част 2 е опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 1_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/2/part-2-C.svg)

<em>2.3 Основна стойност на част 2 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 3 >>](./Part-3.md)

[<< Към предишната страница: Част 1](./Part-1.md)

[Начало](../../README.md)
