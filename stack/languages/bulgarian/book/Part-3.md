## Част 3

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3.svg)

<em>3.0 Част 3 (с възможност за кликване)</em>

### Монтирайте

Методът `componentMount` е една от най-големите части от нашето пътуване! И така, методът, който е интересен за нас, е `ReactCompositeComponent.mountComponent`(1).

Ако си спомняте, споменах, че **първият компонент, който се избутва в дървото на компонента** е `TopLevelWrapper` (вътрешен React клас). Ето, ще го монтираме. Но... това е основно празна обвивка, така че е някак скучно да се отстраняват грешки. Изобщо не засяга потока, така че предлагам да го пропуснем точно сега и да преминем към детето му.

Ето как всъщност работи монтирането на дърво, монтирате родителя, след това неговото дете, дете на дете и т.н. Просто ми повярвайте, след като `TopLevelWrapper` бъде монтиран, детето му (`ReactCompositeComponent`, което управлява компонента `ExampleApplication`) ще бъде поставено в същата фаза.

Добре, връщаме се към стъпка (1). Да видим какво има вътре. Има някои ключови действия, които ще се случат, така че нека обсъдим тази логика с подробности.

### Присвояване на програма за актуализиране на екземпляр

Този `updater` (2), върнат от `transaction.getUpdateQueue()`, всъщност е модулът `ReactUpdateQueue`. И така, защо всъщност е **назначен тук**? Е, тъй като `ReactCompositeComponent` (класът, който разглеждаме в момента) се използва във всички платформи, но актуализаторите са различни, така че ние го присвояваме динамично по време на монтиране в зависимост от платформата.

добре Засега наистина не се нуждаем от това `updater`, но го имайте предвид. `updater` е наистина **важен**, скоро ще бъде използван от добре познатия компонентен метод **`setState`**.

Всъщност не само `updater` се присвоява на екземпляр по време на тази фаза, екземплярът на компонента (вашият персонализиран компонент) също се разширява с `props`, `context` и `refs`.

Вижте кода по-долу:

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#255
// These should be set up in the constructor, but as a convenience for
// simpler class abstractions, we set them up after the fact.
inst.props = publicProps;
inst.context = publicContext;
inst.refs = emptyObject;
inst.updater = updateQueue;
```

Така че тогава можете да получите достъп до `props` във вашия код от екземпляр, като `this.props`.

### Създайте екземпляр на ExampleApplication

Чрез извикване на `_constructComponent` (3) и чрез няколко метода на конструиране, накрая ще бъде създаден `new ExampleApplication()`. Това е моментът, в който ще бъде извикан конструкторът от нашия код. И така, това е първият път, когато нашият код беше действително докоснат от екосистемата на React. хубаво.

### Извършете първоначално монтиране

И така, преминаваме през монтиране (4) и първото нещо, което трябва да се случи тук, е извикване на `componentWillMount` (ако е посочено разбира се). Това е първият метод за куки за жизнения цикъл, който срещаме. Също така, малко по-долу можете да видите `componentDidMount`, но всъщност току-що е избутано в опашката за транзакции, защото не трябва да се извиква директно. Това се случва само в самия край, когато се извършват монтажните операции. Освен това е възможно да добавите `setState` обаждания в `componentWillMount`. В този случай състоянието, разбира се, ще бъде изчислено отново, но без да се извиква методът `render` (това просто няма смисъл, защото компонентът все още не е монтиран).

Официалната документация доказва същото:

> `componentWillMount()` се извиква непосредствено преди монтирането. Извиква се преди `render()`, следователно задаване на състояние в този метод няма да задейства повторно изобразяване.

Нека проверим кода, само за да сме сигурни ;)

```javascript
// \src\renderers\shared\stack\reconciler\ReactCompositeComponent.js#476
if (inst.componentWillMount) {
  //..
  inst.componentWillMount();

  // When mounting, calls to `setState` by `componentWillMount` will set
  // `this._pendingStateQueue` without triggering a re-render.
  if (this._pendingStateQueue) {
    inst.state = this._processPendingState(inst.props, inst.context);
  }
}
```

вярно Добре, но когато `state` се преизчисли, ние извикваме метода `render`. Да, точно този, който сме посочили в нашите компоненти! И така, още едно докосване на „нашия“ код.

Добре, следващото нещо е да създадете екземпляр на React компонент. Ъъъ... пак какво? Изглежда вече сме виждали това `this._instantiateReactComponent`(5) обаждане, нали? Това е вярно, но този път ние инстанцирахме `ReactCompositeComponent` за нашия `ExampleApplication` компонент. Сега ще създадем екземпляри на VDOM за неговото дете въз основа на елемента, който получихме от метода `render`. За нашия точен случай методът за изобразяване връща `div`, така че VDOM представянето за него е `ReactDOMComponent`. Когато екземплярът е създаден, извикваме `ReactReconciler.mountComponent` отново, но този път като `internalInstance`, предаваме новосъздадено копие на `ReactDOMComponent`.

И се обадете на `mountComponent` за това...

### Добре, завършихме _част 3_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-A.svg)

<em>3.1 Част 3 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-B.svg)

<em>3.2 Част 3 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 3_ и да я използваме за окончателната схема `mounting`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/3/part-3-C.svg)

<em>3.3 Основна стойност на част 3 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 4 >>](./Part-4.md)

[<< Към предишната страница: Част 2](./Part-2.md)

[Начало](../../README.md)
