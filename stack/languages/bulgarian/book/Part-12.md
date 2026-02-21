## Част 12

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12.svg)

<em>12.0 Част 12 (с възможност за кликване)</em>

### Ако компонентите наистина трябва да се актуализират..

И така, това е самото начало на актуализацията, това означава, че е добро място за извикване на `componentWillUpdate` hook, ако е посочено (1). След това изобразете повторно компонент и поставете в опашката извикването на още един добре познат метод `componentDidUpdate` (отложете извикването, защото трябва да бъде извикано в самия край на актуализацията).
Какво ще кажете за повторно изобразяване? Всъщност това, което трябва да направим тук, е да извикаме метода `render` на компонента и съответно да актуализираме DOM. И така, първата стъпка, извикваме метод `render`(2) от нашия екземпляр (`ExampleApplication`) и съхраняваме резултата от изобразяването (елементи на React, които са върнати от извикване на метод). След това сравняваме предишния изобразен елемент и виждаме дали DOM действително трябва да се актуализира.

Виждате това, нали, това всъщност е една от убийствените функции на React, избягва излишните DOM актуализации, което прави производителността на React наистина добра.
Поради метода на коментара на кода `shouldUpdateReactComponent`(3):

> „определя дали съществуващият екземпляр трябва да бъде актуализиран, вместо да бъде унищожен или заменен с нов екземпляр“.

Така че, грубо казано, методът проверява дали елементът трябва да бъде напълно заменен, това означава, че старият трябва да бъде първо `unmounted`, след това новият елемент (получен от `render`) трябва да бъде монтиран и маркирането, получено от метода `mount`, трябва да бъде поставено вместо текущия елемент или, ако елементът може да бъде частично актуализиран. Основната причина за пълна замяна на елемент е случай, когато нов елемент е празен (премахнат от логиката `render`) или типът му е различен, напр. беше `div`, но сега е нещо друго. Нека да видим кода, той е достатъчно прост.

```javascript
///src/renderers/shared/shared/shouldUpdateReactComponent.js#25

function shouldUpdateReactComponent(prevElement, nextElement) {
  var prevEmpty = prevElement === null || prevElement === false;
  var nextEmpty = nextElement === null || nextElement === false;
  if (prevEmpty || nextEmpty) {
    return prevEmpty === nextEmpty;
  }

  var prevType = typeof prevElement;
  var nextType = typeof nextElement;
  if (prevType === 'string' || prevType === 'number') {
    return nextType === 'string' || nextType === 'number';
  } else {
    return (
      nextType === 'object' &&
      prevElement.type === nextElement.type &&
      prevElement.key === nextElement.key
    );
  }
}
```

Добре, в случая с нашия `ExampleApplication` току-що актуализирахме свойството `state`, което не засяга толкова много `render`, така че преминаваме към втория сценарий, което означава `update`.

### Добре, завършихме _част 12_.

Нека да обобщим как стигнахме до тук. Нека разгледаме схемата още веднъж, след това премахнем излишните по-маловажни части и тя става следната:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-A.svg)

<em>12.1 Част 12 опростена (с възможност за кликване)</em>

Вероятно също трябва да коригираме интервалите и подравняването:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-B.svg)

<em>12.2 Част 12 опростена и преработена (с възможност за кликване)</em>

хубаво. Всъщност това е всичко, което се случва тук. Така че можем да вземем съществената стойност от _Част 12_ и да я използваме за окончателната схема `updating`:

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/master/stack/images/12/part-12-C.svg)

<em>12.3 Основна стойност на част 12 (с възможност за кликване)</em>

И тогава сме готови!

[Към следващата страница: Част 13 >>](./Part-13.md)

[<< Към предишната страница: Част 11](./Part-11.md)

[Начало](../../README.md)
