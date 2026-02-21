## Въведение

### Схема, първи поглед

[![](../images/intro/all-page-stack-reconciler-25-scale.jpg)](../images/intro/all-page-stack-reconciler.svg)

<em>Въведение.0 Всички схеми (може да се кликне)</em>

Така че... погледнете. Отделете време. Като цяло изглежда сложно, но всъщност описва само два процеса: монтиране и актуализиране. Пропуснах демонтирането, защото това е нещо като "обърнато монтиране" и премахването му опрости схемата. Освен това **това не е 100%** съвпадение на кода, а само основни части, които описват архитектурата. Общо това е около 60% от кода, но останалите 40% биха донесли малка визуална стойност. Така че отново, за простота, го пропуснах.

На пръв поглед вероятно сте забелязали много цветове в схемата. Всеки логически елемент (формата на схемата) е маркиран в цвета на своя родителски модул. Например `methodA` ще бъде червено, ако се извика от `moduleB`, което е червено. По-долу има легенда за модулите в схемата заедно с пътя до всеки файл.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-src-path.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-src-path.svg)

<em>Въведение 1 Цветове на модулите (с възможност за кликване)</em>

Нека ги поставим в схема, за да видим **зависимостите между модулите**.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/files-scheme.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/files-scheme.svg)

<em>Intro.2 Зависимости на модули (с възможност за кликване)</em>

Както вероятно знаете, React е създаден да **поддържа много среди**.

- Мобилен (**ReactNative**)
- Браузър (**ReactDOM**)
- Рендиране на сървъра
- **ReactART** (за рисуване на векторни графики с помощта на React)
- и т.н.

В резултат на това редица файлове всъщност са по-големи, отколкото изглеждат в схемата по-горе. По-долу е същата схема с включена мулти-поддръжка.

[![](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-per-platform-scheme.svg)](https://raw.githubusercontent.com/Bogdan-Lyashenko/Under-the-hood-ReactJS/7c2372e1/stack/images/intro/modules-per-platform-scheme.svg)

<em>Въведение 3 Платформени зависимости (с възможност за кликване)</em>

Както можете да видите, някои елементи изглеждат удвоени. Това показва, че те имат отделна реализация за всяка платформа. Нека вземем нещо просто като ReactEventListener. Очевидно реализацията му ще бъде различна за различните платформи! Технически, както можете да си представите, тези зависими от платформата модули трябва да бъдат по някакъв начин инжектирани или свързани към текущия логически поток и всъщност има много такива инжектори. Тъй като използването им е част от стандартен модел на композиция, избрах да ги пропусна. Отново, за простота.

Нека научим логическия поток за **React DOM** в **обикновен браузър**. Това е най-използваната платформа и напълно покрива всички архитектурни идеи на React. Така че, достатъчно честно!

### Примерен код

Кой е най-добрият начин да научите кода на рамка или библиотека? Точно така, прочетете и отстранете грешки в кода. Добре, ще отстраним грешки в **два процеса**: **ReactDOM.render** и **component.setState**, които се съпоставят при монтиране и актуализиране. Нека да разгледаме кода, който можем да напишем, за да започнем. какво ни трябва Вероятно няколко малки компонента с прости рендери, така че ще бъде по-лесно да се отстраняват грешки.

```javascript
class ChildCmp extends React.Component {
  render() {
    return <div> {this.props.childMessage} </div>;
  }
}

class ExampleApplication extends React.Component {
  constructor(props) {
    super(props);
    this.state = { message: 'no message' };
  }

  componentWillMount() {
    //...
  }

  componentDidMount() {
    /* setTimeout(()=> {
            this.setState({ message: 'timeout state message' });
        }, 1000); */
  }

  shouldComponentUpdate(nextProps, nextState, nextContext) {
    return true;
  }

  componentDidUpdate(prevProps, prevState, prevContext) {
    //...
  }

  componentWillReceiveProps(nextProps) {
    //...
  }

  componentWillUnmount() {
    //...
  }

  onClickHandler() {
    /* this.setState({ message: 'click state message' }); */
  }

  render() {
    return (
      <div>
        <button onClick={this.onClickHandler.bind(this)}>
          {' '}
          set state button{' '}
        </button>
        <ChildCmp childMessage={this.state.message} />
        And some text as well!
      </div>
    );
  }
}

ReactDOM.render(
  <ExampleApplication hello={'world'} />,
  document.getElementById('container'),
  function () {}
);
```

И така, ние сме готови да започнем. Да преминем към първата част от схемата. Един по един ще преминем през всичко това.

[Към следващата страница: Част 0 >>](./Part-0.md)

[Начало](../../README.md)
