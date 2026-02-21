## Част 14

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14.svg)

<em>14.0 Част 14 (с възможност за кликване)</em>

### Последният!

Методът съгласува децата с различните свойства, които засягат съдържанието на децата. Има няколко възможни сценария, но технически само два основни случая. Или децата все още са „сложни“, което означава, че те са компоненти на React и React трябва да рекурсира няколко пъти през техните слоеве, докато най-накрая достигне ниво на съдържание, или децата са прости типове, низове или числа (съдържание).

Превключвателят е тип `nextProps.children`(1) и за нашия случай имаме `ExampleApplication` компонент с три деца: `button`, `ChildCmp` и `text string`.

Добре, нека да видим как работи.

И така, първата итерация с `ExampleApplication children`. Очевидно типът деца не е „съдържание“, така че отиваме със „сложен“ случай. Взимаме всички деца и едно по едно преминаваме през почти същия сценарий, който направихме преди за техния родителски компонент. Между другото, блокирането с проверка `shouldUpdateReactComponent`(2) може да обърка, изглежда дали проверките за проверка се актуализират или не, но в действителност той проверява актуализиране или изтриване и създаване (ние пропускаме НИКАКЪВ клон в схемата, за да бъде проста). Освен това след това сравняваме стари и настоящи деца и ако някое дете е премахнато, демонтираме компонента и го премахваме също.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/children-update.svg)

<em>14.1 Актуализация на деца (може да се кликне)</em>

И така, втората итерация, ние обработваме `button`, това ще бъде простият случай, защото типът на бутона `children` е просто „текст“, тъй като бутонът съдържа само заглавие „бутон за задаване на състояние“. След това проверяваме дали предишният текст е същият като сега, става ли, текстът не е променен, така че не е необходимо да актуализираме `button` тогава? Достатъчно справедливо. И така, „VirtualDOM неща“ в действие. Вече не звучи толкова абстрактно, React поддържа вътрешното представяне на DOM и докосва реалния DOM само ако е необходимо. Отлично представяне като резултат.
И така, мисля, че вече схванахте идеята, тогава поставяме `ChildCmp` за актуализация и неговите деца, докато достигнат елементите от най-ниско ниво (съдържание) и могат да го актуализират. Съдържанието му всъщност е променено, помните, че `this.props.message` се актуализира със „съобщение за състояние на кликване“ чрез `click` и `setState` повикване.

```javascript
//...
onClickHandler() {
	this.setState({ message: 'click state message' });
}

render() {
    return <div>
		<button onClick={this.onClickHandler.bind(this)}>set state button</button>
		<ChildCmp childMessage={this.state.message} />
//...

```

И така, да видим. Ще актуализираме съдържанието на елемента, всъщност - ще го заменим. Е, каква всъщност е актуализацията? Така че това е вид конфигурационен обект, който ще бъде анализиран и конфигурираното действие ще бъде приложено. За нашия случай с текстова актуализация изглежда така:

```javascript
{
  afterNode: null,
  content: "click state message",
  fromIndex: null,
  fromNode: null,
  toIndex: null,
  type: "TEXT_CONTENT"
}
```

Виждате, че е почти празен, случаят с текстовата актуализация е доста ясен. Както можете да видите, има много свойства, това е така, защото когато вие, нека преместим възли, това може да бъде по-сложно от просто актуализиране на текст.

Вижте кода на метода, за да имате ясна картина.

```javascript
//src\renderers\dom\client\utils\DOMChildrenOperations.js#172
processUpdates: function(parentNode, updates) {
    for (var k = 0; k < updates.length; k++) {
      var update = updates[k];

      switch (update.type) {
        case 'INSERT_MARKUP':
          insertLazyTreeChildAt(
            parentNode,
            update.content,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'MOVE_EXISTING':
          moveChild(
            parentNode,
            update.fromNode,
            getNodeAfter(parentNode, update.afterNode)
          );
          break;
        case 'SET_MARKUP':
          setInnerHTML(
            parentNode,
            update.content
          );
          break;
        case 'TEXT_CONTENT':
          setTextContent(
            parentNode,
            update.content
          );
          break;
        case 'REMOVE_NODE':
          removeChild(parentNode, update.fromNode);
          break;
      }
    }
  }
```

Нашият случай е „TEXT_CONTENT“ и всъщност това е последната стъпка, извикваме `setTextContent` (3) и променяме съдържанието на HTML възела (истинския, от DOM).

браво! Съдържанието се актуализира и на страницата се изобразява отново и за потребителя. Какво друго е пропуснато? Нека завършим нашата актуализация! Всичко е готово, така че куката `componentDidUpdate` на нашия компонент ще бъде извикана. Как обикновено се извикват отложените обратни повиквания? Точно така, с обвивка на транзакция. Както си спомняте, мръсната актуализация на компонента беше обвита с `ReactUpdatesFlushTransaction` и една от нейните обвивки всъщност съдържа логика `this.callbackQueue.notifyAll()`, така че ще извика `componentDidUpdate`. хубаво!

Изглежда, че сме готови. Напълно.

### Добре, завършихме _част 14_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-A.svg)

<em>14.2 Част 14 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-B.svg)

<em>14.3 Част 14 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 14_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/part-14-C.svg)

<em>14.4 Основна стойност на част 14 (с възможност за кликване)</em>

И тогава сме готови! Всъщност приключихме с актуализирането. Нека го видим по-долу!

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/14/updating-parts-C.svg)

<em>14.5 Актуализиране (с възможност за кликване)</em>

[<< Към предишната страница: Част 13](./Part-13.md)

[Начало](../../README.md)
