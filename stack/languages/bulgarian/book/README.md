# Под капака: React
<em>Това хранилище съдържа обяснение как React работи отвътре. Докато дебъгвах целия код, структурирах логиката във визуални блок-схеми, анализирах ги и обобщих основните концепции и подходи. Частта за Stack версията е завършена, а Fiber е в процес.</em>

> Най-удобно за четене: [github-pages website](https://bogdan-lyashenko.github.io/Under-the-hood-ReactJS/).

> Ако имаш идея за подобрение, отвори issue.

Всяка схема може да се отвори в нов таб. Ползвай това за zoom и дръж текста и схемата в отделни табове, за да следиш по-лесно потока на кода.

Тук разглеждаме и двете версии на React: текущата със Stack reconciler и следващата с Fiber. За "legacy React" се ползва [React v15.4.2](https://github.com/facebook/react/tree/v15.4.2), а за Fiber се разглежда линията v16.

## Stack reconciler
[![](../../../../stack/images/intro/all-page-stack-reconciler-25-scale.jpg)](../../../../stack/images/intro/all-page-stack-reconciler.svg)

Цялата схема е разделена на 15 части:

* [Intro](./Intro.md)
* [Part 0](./Part-0.md)
* [Part 1](./Part-1.md)
* [Part 2](./Part-2.md)
* [Part 3](./Part-3.md)
* [Part 4](./Part-4.md)
* [Part 5](./Part-5.md)
* [Part 6](./Part-6.md)
* [Part 7](./Part-7.md)
* [Part 8](./Part-8.md)
* [Part 9](./Part-9.md)
* [Part 10](./Part-10.md)
* [Part 11](./Part-11.md)
* [Part 12](./Part-12.md)
* [Part 13](./Part-13.md)
* [Part 14](./Part-14.md)

## Fiber
1. [Intro](../../../../fiber/book/Intro.md) [TODO]
