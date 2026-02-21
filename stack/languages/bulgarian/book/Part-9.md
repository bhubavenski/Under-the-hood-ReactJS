## Част 9

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9.svg)

<em>9.0 Част 9 (с възможност за кликване)</em>

### Но да се върнем назад..

Както забелязахте в схемата, извикването на метода `setState` може да бъде задействано по няколко начина, по-точно, със или без външно въздействие (означава „действие на потребителя“). Да вземем два случая: в първия случай извикването на метода се задейства чрез щракване на мишката, а вторият, просто извикване от `setTimeout` в `componentDidMount`.

Какво всъщност прави тази разлика? Е, както си спомняте, React обработва актуализации в `batches`, това означава, че списъкът с актуализации трябва да бъде събран по някакъв начин и след това `flushed`. Работата е там, че когато се появи събитие на мишката, то се обработва на най-високо ниво и след това чрез няколко слоя обвивки ще започне пакетната актуализация. Между другото, както можете да видите, това се случва само ако `ReactEventListener` е `enabled` (1) и, ако си спомняте, по време на фаза на монтиране на компонент, един от `ReactReconcileTransaction` обвивките всъщност го деактивира и прави монтирането безопасно. Стига умно! Но какво да кажем за случая `setTimeout`? Освен това е просто, преди да поставите компонент в списъка `dirtyComponents`, React ще се увери, че транзакцията е стартирана (отворена), така че след това, по-късно, тя трябва да бъде затворена и актуализациите изчистени.

Както знаете, React имплементира „синтетични събития“, някаква „синтактична захар“, която всъщност обвива нативните събития. Но след това, по-късно, те все още се опитват да се държат така, както всички сме виждали събитията. Можете да видите коментара в кода:

> „За да подпомогнем развитието, можем да получим по-добра интеграция на инструмента за разработка чрез симулиране на реално събитие в браузъра“

```javascript
var fakeNode = document.createElement('react');

ReactErrorUtils.invokeGuardedCallback = function (name, func, a) {
  var boundFunc = func.bind(null, a);
  var evtType = 'react-' + name;

  fakeNode.addEventListener(evtType, boundFunc, false);

  var evt = document.createEvent('Event');
  evt.initEvent(evtType, false, false);

  fakeNode.dispatchEvent(evt);
  fakeNode.removeEventListener(evtType, boundFunc, false);
};
```

Добре, обратно към нашата актуализация, нека видим още веднъж. Подходът е:

1. извикайте setState
1. отворете транзакция за пакетиране, ако все още не е отворена
1. добавете засегнатите компоненти към списъка `dirtyComponents`,
1. затворете транзакцията с извикване на `ReactUpdates.flushBatchedUpdates`, което всъщност означава „обработка на всичко, което е събрано в `dirtyComponents`“.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/set-state-update-start.svg)

<em>9.1 `setState` начало (с възможност за кликване)</em>

### Добре, завършихме _част 9_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-A.svg)

<em>9.2 Част 9 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-B.svg)

<em>9.3 Част 9 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 9_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/9/part-9-C.svg)

<em>9.6 Съществена стойност на част 9 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 10 >>](./Part-10.md)

[<< Към предишната страница: Част 8](./Part-8.md)

[Начало](../../README.md)
