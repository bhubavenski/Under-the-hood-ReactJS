## Част 8

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8.svg)

<em>8.0 Част 8 (с възможност за кликване)</em>

### `this.setState`

Знаем как работи монтирането, но сега нека преминем от другата страна. Да, метод `setState`, още едно парче торта!

Първо, защо всъщност можем да извикаме някакъв метод, наречен `setState`? Е, това е достатъчно ясно, ние наследихме нашия компонент от `ReactComponent`. Добре тогава, лесно е да намерите този клас в източника на React и да проверите неговия `setState` метод.

```javascript
//src\isomorphic\modern\class\ReactComponent.js#68
this.updater.enqueueSetState(this, partialState);
```

Както можете да видите, има някакъв `updater` интерфейс. Какво е това `updater`? Е, ако проверите процеса на монтиране, който току-що анализирахме, по време на `mountComponent`, екземплярът получава свойството `updater` като препратка към `ReactUpdateQueue` (`src\renderers\shared\stack\reconciler\ReactUpdateQueue.js`).

Е, потопете се в метода `enqueueSetState` (1) и вижте, че в началото той избутва частично състояние (частичното състояние е обект, който предавате в `this.setState`) до `_pendingStateQueue` (2) на вътрешния екземпляр (само да напомня: публичен екземпляр, това всъщност е нашият персонализиран компонент `ExampleApplication` и вътрешният екземпляр е `ReactCompositeComponent`, което беше създаден по време на монтиране), вторично, ние `enqueueUpdate`, какво всъщност проверяваме дали актуализациите вече са в ход и изпращаме нашия компонент към списъка `dirtyComponents`, в противен случай, ако не - стартираме транзакция за актуализиране и след това изпращаме компонента към списъка `dirtyComponents`.

За да обобщим това, всеки компонент има собствен списък с чакащи състояния, което означава, че всеки път, когато извикате `setState` в една транзакция, вие просто натискате тези обекти в опашка, след което по-късно те ще бъдат обединени в състояние на компонент един по един. И когато извикате `setState`, вие добавяте своя компонент в списъка `dirtyComponents`. Вероятно вече се чудите как се обработват тези `dirtyComponents`? Прав си, това е следващото важно парче от пъзела...

### Добре, завършихме _част 8_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-A.svg)

<em>8.1 Част 8 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-B.svg)

<em>8.2 Част 8 е опростена и преработена (с възможност за щракване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 8_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/8/part-8-C.svg)

<em>8.3 Основна стойност на част 8 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 9 >>](./Part-9.md)

[<< Към предишната страница: Част 7](./Part-7.md)

[Начало](../../README.md)
